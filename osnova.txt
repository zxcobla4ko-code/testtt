-- TIDEX HUB KEY SYSTEM
-- STANDALONE | Host on GitHub | jnkie API verify

local HttpService      = game:GetService("HttpService")
local TweenService     = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local Players          = game:GetService("Players")
local LocalPlayer      = Players.LocalPlayer
local RunService       = game:GetService("RunService")

local Config = {
    api         = "f3559cc2-bf87-42a2-885a-139633d6ce10",
    service     = "oldwt-tidexhub",
    provider    = "oldwt",
    keyLink     = "https://jnkie.com/flow/b3ec74ca-3d15-4c75-8e33-6dbcca778acd",
    keyFileName = "TidexHubKey.txt",
    scriptUrl   = "loadstring(game:HttpGet("https://raw.githubusercontent.com/zxcobla4ko-code/test/refs/heads/main/test"))()",
}

local guiAlive = true

local function SaveKey(key)
    pcall(function() writefile(Config.keyFileName, key) end)
end
local function LoadKey()
    local key = ""
    pcall(function()
        if isfile and isfile(Config.keyFileName) then
            key = readfile(Config.keyFileName) or ""
        end
    end)
    return key:gsub("%s+", "")
end

-- jnkie API — работает в standalone, НЕ работает в UI-Source
local function VerifyKey(key)
    local url = string.format(
        "https://api.jnkie.com/api/check?key=%s&service=%s&provider=%s&apikey=%s",
        HttpService:UrlEncode(key),
        HttpService:UrlEncode(Config.service),
        HttpService:UrlEncode(Config.provider),
        HttpService:UrlEncode(Config.api)
    )
    local ok, result = pcall(function()
        return HttpService:GetAsync(url, true)
    end)
    if not ok then return false, "Connection error" end
    local valid, msg = false, "Invalid key"
    pcall(function()
        local d = HttpService:JSONDecode(result)
        if type(d) == "table" then
            valid = d.valid == true
            msg   = d.message or d.error or "Invalid key"
        end
    end)
    return valid, msg
end

local function GetGameName()
    local n = "Unknown"
    pcall(function() n = game:GetService("MarketplaceService"):GetProductInfo(game.PlaceId).Name end)
    return n
end
local function GetExecutor()
    if syn then return "Synapse" end
    if getgenv and getgenv()["LPH_".. "NO_CODEJAIL"] then return "ProtoSmasher" end
    if KRNL_LOADED then return "KRNL" end
    if Fluxus then return "Fluxus" end
    if getexecutorname then return getexecutorname() end
    return "Unknown"
end
local function Create(class, props, parent)
    local i = Instance.new(class)
    for k,v in pairs(props or {}) do pcall(function() i[k]=v end) end
    if parent then i.Parent=parent end
    return i
end
local function Tw(props, inst, dur, style)
    dur=dur or 0.35; style=style or Enum.EasingStyle.Quint
    local t=TweenService:Create(inst,TweenInfo.new(dur,style,Enum.EasingDirection.Out),props)
    t:Play(); return t
end
local function AddRipple(btn,x,y)
    local r=Create("Frame",{Size=UDim2.new(0,0,0,0),Position=UDim2.new(0,x,0,y),AnchorPoint=Vector2.new(0.5,0.5),BackgroundColor3=Color3.fromRGB(200,200,200),BackgroundTransparency=0.6,BorderSizePixel=0,ZIndex=5},btn)
    Tw({Size=UDim2.new(0,250,0,250),BackgroundTransparency=1},r,0.5,Enum.EasingStyle.Quart)
    task.delay(0.5,function() if r.Parent then r:Destroy() end end)
end

local gameName=GetGameName(); local executorName=GetExecutor()

local ScreenGui=Create("ScreenGui",{Name="TidexAuth",ResetOnSpawn=false,ZIndexBehavior=Enum.ZIndexBehavior.Sibling,IgnoreGuiInset=true},game.CoreGui)
local Blur=Create("BlurEffect",{Name="AuthBlur",Size=0},game.Lighting)
local Bg=Create("Frame",{Size=UDim2.new(1,0,1,0),BackgroundColor3=Color3.fromRGB(0,0,0),BorderSizePixel=0},ScreenGui)
Create("UIGradient",{Color=ColorSequence.new({ColorSequenceKeypoint.new(0,Color3.fromRGB(0,0,0)),ColorSequenceKeypoint.new(0.4,Color3.fromRGB(8,8,8)),ColorSequenceKeypoint.new(0.6,Color3.fromRGB(8,8,8)),ColorSequenceKeypoint.new(1,Color3.fromRGB(0,0,0))}),Rotation=180},Bg)
local GrainOverlay=Create("ImageLabel",{Size=UDim2.new(1.1,0,1.1,0),Position=UDim2.new(-0.05,0,-0.05,0),BackgroundTransparency=1,Image="rbxassetid://2148766655",ImageTransparency=0.94,ScaleType=Enum.ScaleType.Tile,TileSize=UDim2.new(0,128,0,128)},Bg)
local Vignette=Create("ImageLabel",{Size=UDim2.new(1.3,0,1.3,0),Position=UDim2.new(-0.15,0,-0.15,0),BackgroundTransparency=1,Image="rbxassetid://1313450459",ImageTransparency=0.15,ImageColor3=Color3.fromRGB(0,0,0),ScaleType=Enum.ScaleType.Slice,SliceCenter=Rect.new(0,0,0,0)},Bg)
local ScanLines=Create("Frame",{Size=UDim2.new(1,0,1,0),BackgroundTransparency=1},Bg)
for i=0,80 do Create("Frame",{Size=UDim2.new(1,0,0,1),Position=UDim2.new(0,0,0,i*13),BackgroundColor3=Color3.fromRGB(255,255,255),BackgroundTransparency=0.97,BorderSizePixel=0},ScanLines) end
local Particles=Create("Frame",{Size=UDim2.new(1,0,1,0),BackgroundTransparency=1},Bg)
task.spawn(function()
    while guiAlive and Particles and Particles.Parent do
        local p=Create("Frame",{Size=UDim2.new(0,math.random(2,3),0,math.random(2,3)),Position=UDim2.new(math.random()*1.2-0.1,0,math.random()*1.2-0.1,0),BackgroundColor3=Color3.fromRGB(255,255,255),BackgroundTransparency=math.random(40,60)/100,BorderSizePixel=0},Particles)
        local life=math.random(5,10)
        Tw({BackgroundTransparency=1},p,life,Enum.EasingStyle.Sine)
        Tw({Position=UDim2.new(0,p.Position.X.Offset+math.random(-80,80),0,p.Position.Y.Offset+math.random(-60,60))},p,life,Enum.EasingStyle.Sine)
        task.delay(life,function() if p and p.Parent then p:Destroy() end end)
        task.wait(math.random(0.2,0.5))
    end
end)

local Card=Create("Frame",{Size=UDim2.new(0,440,0,420),Position=UDim2.new(0.5,0,0.5,0),AnchorPoint=Vector2.new(0.5,0.5),BackgroundColor3=Color3.fromRGB(12,12,12),BorderSizePixel=0,ClipsDescendants=true},ScreenGui)
Create("UIStroke",{Color=Color3.fromRGB(40,40,40),Thickness=1},Card)
Create("Frame",{Size=UDim2.new(1,0,0,1),BackgroundColor3=Color3.fromRGB(255,255,255),BorderSizePixel=0},Card)
Create("Frame",{Size=UDim2.new(1,0,0,1),Position=UDim2.new(0,0,1,-1),BackgroundColor3=Color3.fromRGB(255,255,255),BorderSizePixel=0},Card)
Create("Frame",{Size=UDim2.new(0,1,1,0),BackgroundColor3=Color3.fromRGB(255,255,255),BackgroundTransparency=0.9,BorderSizePixel=0},Card)
Create("Frame",{Size=UDim2.new(0,1,1,0),Position=UDim2.new(1,-1,0,0),BackgroundColor3=Color3.fromRGB(255,255,255),BackgroundTransparency=0.9,BorderSizePixel=0},Card)

local Header=Create("Frame",{Size=UDim2.new(1,0,0,100),BackgroundTransparency=1},Card)
local LogoContainer=Create("Frame",{Size=UDim2.new(0,64,0,64),Position=UDim2.new(0.5,0,0,10),AnchorPoint=Vector2.new(0.5,0),BackgroundColor3=Color3.fromRGB(255,255,255),BorderSizePixel=0},Header)
Create("Frame",{Size=UDim2.new(1,-8,1,-8),Position=UDim2.new(0,4,0,4),BackgroundColor3=Color3.fromRGB(12,12,12),BorderSizePixel=0},LogoContainer)
Create("TextLabel",{Size=UDim2.new(1,0,1,0),BackgroundTransparency=1,Text="W",TextColor3=Color3.fromRGB(255,255,255),Font=Enum.Font.GothamBold,TextSize=28,TextTransparency=0},LogoContainer)
local LogoClickBtn=Create("TextButton",{Size=UDim2.new(1,0,1,0),BackgroundTransparency=1,Text="",ZIndex=3},LogoContainer)
local LogoHover=Create("Frame",{Size=UDim2.new(1,-4,1,-4),Position=UDim2.new(0,2,0,2),BackgroundColor3=Color3.fromRGB(255,255,255),BackgroundTransparency=1,BorderSizePixel=0,ZIndex=2},LogoContainer)
Create("TextLabel",{Size=UDim2.new(1,0,0,16),Position=UDim2.new(0,0,0,80),BackgroundTransparency=1,Text="T I D E X   H U B",TextColor3=Color3.fromRGB(60,60,60),Font=Enum.Font.GothamBold,TextSize=10},Header)
Create("Frame",{Size=UDim2.new(0.9,0,0,1),Position=UDim2.new(0.05,0,0,103),BackgroundColor3=Color3.fromRGB(25,25,25),BorderSizePixel=0},Card)

local PageContainer=Create("Frame",{Size=UDim2.new(1,0,1,-105),Position=UDim2.new(0,0,0,105),BackgroundTransparency=1,ClipsDescendants=true},Card)
local KeyPage=Create("Frame",{Size=UDim2.new(1,0,1,0),BackgroundTransparency=1,ClipsDescendants=true},PageContainer)
local Content=Create("Frame",{Size=UDim2.new(1,-40,0,230),Position=UDim2.new(0,20,0,10),BackgroundTransparency=1},KeyPage)

local StatusRow=Create("Frame",{Size=UDim2.new(1,0,0,20),BackgroundTransparency=1},Content)
local StatusDot=Create("Frame",{Size=UDim2.new(0,6,0,6),Position=UDim2.new(0,0,0.5,-3),BackgroundColor3=Color3.fromRGB(80,80,80),BorderSizePixel=0},StatusRow)
local StatusLabel=Create("TextLabel",{Size=UDim2.new(1,-14,1,0),Position=UDim2.new(0,14,0,0),BackgroundTransparency=1,Text="Awaiting input",TextColor3=Color3.fromRGB(60,60,60),Font=Enum.Font.Gotham,TextSize=10,TextXAlignment=Enum.TextXAlignment.Left},StatusRow)

local InputWrapper=Create("Frame",{Size=UDim2.new(1,0,0,46),Position=UDim2.new(0,0,0,28),BackgroundColor3=Color3.fromRGB(8,8,8),BorderSizePixel=0},Content)
local InputStroke=Create("UIStroke",{Color=Color3.fromRGB(30,30,30),Thickness=1},InputWrapper)
local InputBox=Create("TextBox",{Size=UDim2.new(1,-20,1,0),Position=UDim2.new(0,10,0,0),BackgroundTransparency=1,Text="",PlaceholderText="Enter license key",PlaceholderColor3=Color3.fromRGB(40,40,40),TextColor3=Color3.fromRGB(220,220,220),Font=Enum.Font.Gotham,TextSize=13,TextXAlignment=Enum.TextXAlignment.Left,ClearTextOnFocus=false},InputWrapper)

local VerifyBtn=Create("TextButton",{Size=UDim2.new(1,0,0,44),Position=UDim2.new(0,0,0,84),BackgroundColor3=Color3.fromRGB(255,255,255),BorderSizePixel=0,Text="VERIFY",TextColor3=Color3.fromRGB(0,0,0),Font=Enum.Font.GothamBold,TextSize=12},Content)
local GetKeyLink=Create("TextButton",{Size=UDim2.new(1,0,0,28),Position=UDim2.new(0,0,0,138),BackgroundTransparency=1,Text="Get key  →  jnkie checkpoint",TextColor3=Color3.fromRGB(45,45,45),Font=Enum.Font.Gotham,TextSize=10},Content)
local Underline=Create("Frame",{Size=UDim2.new(0,0,0,1),Position=UDim2.new(0,0,1,0),BackgroundColor3=Color3.fromRGB(80,80,80),BorderSizePixel=0},GetKeyLink)

local InfoGrid=Create("Frame",{Size=UDim2.new(1,0,0,40),Position=UDim2.new(0,0,0,175),BackgroundTransparency=1},Content)
Create("UIGridLayout",{CellSize=UDim2.new(0.5,-4,0,18),CellPadding=UDim2.new(0,8,0,4),SortOrder=Enum.SortOrder.LayoutOrder},InfoGrid)
local function MakeInfoCell(label,value,order)
    local cell=Create("Frame",{BackgroundColor3=Color3.fromRGB(8,8,8),BorderSizePixel=0,LayoutOrder=order},InfoGrid)
    Create("TextLabel",{Size=UDim2.new(0,40,1,0),Position=UDim2.new(0,6,0,0),BackgroundTransparency=1,Text=label,TextColor3=Color3.fromRGB(35,35,35),Font=Enum.Font.Gotham,TextSize=8,TextXAlignment=Enum.TextXAlignment.Left},cell)
    Create("TextLabel",{Size=UDim2.new(1,-52,1,0),Position=UDim2.new(0,48,0,0),BackgroundTransparency=1,Text=value,TextColor3=Color3.fromRGB(100,100,100),Font=Enum.Font.GothamBold,TextSize=8,TextXAlignment=Enum.TextXAlignment.Right,TextTruncate=Enum.TextTruncate.AtEnd},cell)
end
MakeInfoCell("USER",LocalPlayer.Name,0); MakeInfoCell("EXEC",executorName,1)
MakeInfoCell("HWID","ID_"..tostring(LocalPlayer.UserId),2); MakeInfoCell("GAME",gameName,3)

local OverlayContainer=Create("Frame",{Size=UDim2.new(1,0,1,0),BackgroundTransparency=1,ZIndex=10,Visible=false},KeyPage)
local LoadingOvl=Create("Frame",{Size=UDim2.new(1,0,1,0),BackgroundColor3=Color3.fromRGB(12,12,12),BackgroundTransparency=1,Visible=false},OverlayContainer)
local Spinner=Create("Frame",{Size=UDim2.new(0,36,0,36),Position=UDim2.new(0.5,0,0.42,0),AnchorPoint=Vector2.new(0.5,0),BackgroundTransparency=1},LoadingOvl)
local spinnerDots={}
for i=1,12 do
    local dot=Create("Frame",{Size=UDim2.new(0,3,0,3),AnchorPoint=Vector2.new(0.5,0.5),BackgroundColor3=Color3.fromRGB(255,255,255),BackgroundTransparency=0.2+(i-1)*0.07,BorderSizePixel=0},Spinner)
    table.insert(spinnerDots,{dot=dot,angle=(i-1)*30})
end
task.spawn(function()
    while guiAlive do
        if LoadingOvl.Visible then
            for _,d in ipairs(spinnerDots) do
                local a=math.rad(d.angle+tick()*150)
                d.dot.Position=UDim2.new(0.5+math.cos(a)*0.4,0,0.5+math.sin(a)*0.4,0)
            end
        end
        RunService.Heartbeat:Wait()
    end
end)
Create("TextLabel",{Size=UDim2.new(1,0,0,18),Position=UDim2.new(0,0,0.56,0),BackgroundTransparency=1,Text="Verifying license",TextColor3=Color3.fromRGB(80,80,80),Font=Enum.Font.Gotham,TextSize=10},LoadingOvl)

local SuccessOvl=Create("Frame",{Size=UDim2.new(1,0,1,0),BackgroundColor3=Color3.fromRGB(12,12,12),BackgroundTransparency=1,Visible=false},OverlayContainer)
local CheckSvg=Create("TextLabel",{Size=UDim2.new(0,50,0,50),Position=UDim2.new(0.5,0,0.35,0),AnchorPoint=Vector2.new(0.5,0),BackgroundTransparency=1,Text="+",TextColor3=Color3.fromRGB(255,255,255),Font=Enum.Font.GothamBold,TextSize=34,Rotation=45},SuccessOvl)
Create("TextLabel",{Size=UDim2.new(1,0,0,18),Position=UDim2.new(0,0,0.54,0),BackgroundTransparency=1,Text="LICENSE VERIFIED",TextColor3=Color3.fromRGB(255,255,255),Font=Enum.Font.GothamBold,TextSize=14},SuccessOvl)
local SuccessSub=Create("TextLabel",{Size=UDim2.new(1,0,0,14),Position=UDim2.new(0,0,0.60,0),BackgroundTransparency=1,Text="Initializing",TextColor3=Color3.fromRGB(50,50,50),Font=Enum.Font.Gotham,TextSize=10},SuccessOvl)

local ErrorOvl=Create("Frame",{Size=UDim2.new(1,0,1,0),BackgroundColor3=Color3.fromRGB(12,12,12),BackgroundTransparency=1,Visible=false},OverlayContainer)
Create("TextLabel",{Size=UDim2.new(0,50,0,50),Position=UDim2.new(0.5,0,0.28,0),AnchorPoint=Vector2.new(0.5,0),BackgroundTransparency=1,Text="X",TextColor3=Color3.fromRGB(255,255,255),Font=Enum.Font.GothamBold,TextSize=32},ErrorOvl)
local ErrorTitle=Create("TextLabel",{Size=UDim2.new(1,0,0,18),Position=UDim2.new(0,0,0.48,0),BackgroundTransparency=1,Text="INVALID LICENSE",TextColor3=Color3.fromRGB(255,255,255),Font=Enum.Font.GothamBold,TextSize=14},ErrorOvl)
local ErrorSub=Create("TextLabel",{Size=UDim2.new(1,-20,0,28),Position=UDim2.new(0,10,0.54,0),BackgroundTransparency=1,Text="Check your key and retry",TextColor3=Color3.fromRGB(50,50,50),Font=Enum.Font.Gotham,TextSize=10,TextWrapped=true},ErrorOvl)
local RetryBtn=Create("TextButton",{Size=UDim2.new(0,90,0,30),Position=UDim2.new(0.5,-45,0.64,0),BackgroundColor3=Color3.fromRGB(255,255,255),BorderSizePixel=0,Text="RETRY",TextColor3=Color3.fromRGB(0,0,0),Font=Enum.Font.GothamBold,TextSize=10},ErrorOvl)

local InfoPage=Create("Frame",{Size=UDim2.new(1,0,1,0),Position=UDim2.new(1,0,0,0),BackgroundTransparency=1,ClipsDescendants=true,Visible=false},PageContainer)
local InfoBg=Create("Frame",{Size=UDim2.new(1,0,1,0),BackgroundColor3=Color3.fromRGB(0,0,0),BorderSizePixel=0},InfoPage)
Create("UIStroke",{Color=Color3.fromRGB(25,25,25),Thickness=1},InfoBg)
local BackBtn=Create("TextButton",{Size=UDim2.new(0,80,0,28),Position=UDim2.new(0,15,0,12),BackgroundColor3=Color3.fromRGB(255,255,255),BorderSizePixel=0,Text="BACK",TextColor3=Color3.fromRGB(0,0,0),Font=Enum.Font.GothamBold,TextSize=9},InfoBg)
local InfoContent=Create("Frame",{Size=UDim2.new(1,-30,1,-60),Position=UDim2.new(0,15,0,50),BackgroundTransparency=1},InfoBg)
Create("TextLabel",{Size=UDim2.new(1,0,0,20),BackgroundTransparency=1,Text="A B O U T",TextColor3=Color3.fromRGB(60,60,60),Font=Enum.Font.GothamBold,TextSize=10},InfoContent)
Create("Frame",{Size=UDim2.new(1,0,0,1),Position=UDim2.new(0,0,0,23),BackgroundColor3=Color3.fromRGB(20,20,20),BorderSizePixel=0},InfoContent)
Create("TextLabel",{Size=UDim2.new(1,0,0,80),Position=UDim2.new(0,0,0,33),BackgroundTransparency=1,Text="Скрипт разрабатывался только двумя людьми (xavierpihta and synapse1337). Мы очень благодарны вам за то, что вы используете наш скрипт! Надеемся, что он вам понравится.",TextColor3=Color3.fromRGB(60,60,60),Font=Enum.Font.Gotham,TextSize=11,TextXAlignment=Enum.TextXAlignment.Left,TextWrapped=true,LineHeight=1.5},InfoContent)
Create("TextLabel",{Size=UDim2.new(1,0,0,24),Position=UDim2.new(0,0,0,117),BackgroundTransparency=1,Text="Ощутите ностальгию старого War Tycoon.",TextColor3=Color3.fromRGB(50,50,50),Font=Enum.Font.Gotham,TextSize=11,TextXAlignment=Enum.TextXAlignment.Left,TextWrapped=true,LineHeight=1.5},InfoContent)
local InfoWarning=Create("Frame",{Size=UDim2.new(1,0,0,50),Position=UDim2.new(0,0,0,151),BackgroundColor3=Color3.fromRGB(5,5,5),BorderSizePixel=0},InfoContent)
Create("UIStroke",{Color=Color3.fromRGB(25,25,25),Thickness=1},InfoWarning)
Create("TextLabel",{Size=UDim2.new(1,-16,1,0),Position=UDim2.new(0,8,0,0),BackgroundTransparency=1,Text="Важно! В скрипте есть IP-логгер, который используется для безопасности нашего скрипта.",TextColor3=Color3.fromRGB(50,50,50),Font=Enum.Font.Gotham,TextSize=10,TextXAlignment=Enum.TextXAlignment.Left,TextWrapped=true,LineHeight=1.4},InfoWarning)

local Footer=Create("Frame",{Size=UDim2.new(1,0,0,30),Position=UDim2.new(0,0,1,-30),BackgroundTransparency=1},Card)
Create("TextLabel",{Size=UDim2.new(0.5,0,1,0),Position=UDim2.new(0,15,0,0),BackgroundTransparency=1,Text="old war tycoon",TextColor3=Color3.fromRGB(30,30,30),Font=Enum.Font.Gotham,TextSize=8,TextXAlignment=Enum.TextXAlignment.Left},Footer)
Create("TextLabel",{Size=UDim2.new(0.4,0,1,0),Position=UDim2.new(0.6,0,0,0),BackgroundTransparency=1,Text="v1.0.0",TextColor3=Color3.fromRGB(30,30,30),Font=Enum.Font.Gotham,TextSize=8,TextXAlignment=Enum.TextXAlignment.Right},Footer)
local CloseBtn=Create("TextButton",{Size=UDim2.new(0,20,0,20),Position=UDim2.new(1,-30,0,15),BackgroundTransparency=1,Text="+",TextColor3=Color3.fromRGB(50,50,50),Font=Enum.Font.GothamBold,TextSize=14,Rotation=45,ZIndex=10},Card)

local corners={}
for _,d in ipairs({{0,8,0,8,16,1},{0,8,0,8,1,16},{1,-24,0,8,16,1},{1,-9,0,8,1,16},{0,8,1,-9,16,1},{0,8,1,-24,1,16},{1,-24,1,-9,16,1},{1,-9,1,-24,1,16}}) do
    table.insert(corners,Create("Frame",{Size=UDim2.new(0,d[5],0,d[6]),Position=UDim2.new(d[1],d[2],d[3],d[4]),BackgroundColor3=Color3.fromRGB(255,255,255),BackgroundTransparency=0.7,BorderSizePixel=0,ZIndex=5},Card))
end
task.spawn(function()
    while guiAlive and Card and Card.Parent do
        local a=math.sin(tick()*2.5)*0.2+0.7
        for _,c in ipairs(corners) do Tw({BackgroundTransparency=a},c,0.1) end
        task.wait(0.1)
    end
end)
local ScanBar=Create("Frame",{Size=UDim2.new(1,0,0,1),BackgroundColor3=Color3.fromRGB(255,255,255),BackgroundTransparency=0.92,BorderSizePixel=0,ZIndex=5},Card)
task.spawn(function()
    while guiAlive and Card and Card.Parent do
        Tw({Position=UDim2.new(0,0,0,0)},ScanBar,3,Enum.EasingStyle.Sine) task.wait(3)
        Tw({Position=UDim2.new(0,0,1,-1)},ScanBar,3,Enum.EasingStyle.Sine) task.wait(3)
    end
end)

local isOnInfoPage,isAnimating,ovlBusy=false,false,false

local function SetStatus(text,color)
    StatusLabel.Text=text
    if color then Tw({TextColor3=color},StatusLabel,0.25);Tw({BackgroundColor3=color},StatusDot,0.25) end
end
local function HideAllOverlays()
    ovlBusy=true;OverlayContainer.Visible=false
    for _,o in ipairs({LoadingOvl,SuccessOvl,ErrorOvl}) do o.BackgroundTransparency=1;o.Visible=false end
    ovlBusy=false
end
local function ShowOverlay(name)
    while ovlBusy do task.wait(0.01) end;ovlBusy=true
    HideAllOverlays();task.wait(0.05)
    local ovl=name=="loading" and LoadingOvl or name=="success" and SuccessOvl or ErrorOvl
    ovl.BackgroundTransparency=1;ovl.Visible=true;OverlayContainer.Visible=true
    Tw({BackgroundTransparency=0},ovl,0.35);task.wait(0.4);ovlBusy=false
end
local function HideOverlay(name)
    while ovlBusy do task.wait(0.01) end;ovlBusy=true
    local ovl=name=="loading" and LoadingOvl or name=="success" and SuccessOvl or ErrorOvl
    Tw({BackgroundTransparency=1},ovl,0.3);task.wait(0.35);ovl.Visible=false;OverlayContainer.Visible=false;ovlBusy=false
end
local function CloseGUI()
    guiAlive=false
    local t=Tw({Size=UDim2.new(0,440,0,0)},Card,0.4,Enum.EasingStyle.Quint)
    t.Completed:Connect(function()
        Card.Visible=false
        for _,obj in ipairs({ScanLines,GrainOverlay,Vignette,Particles}) do if obj and obj.Parent then obj:Destroy() end end
        local t2=Tw({BackgroundTransparency=1},Bg,0.35);Tw({Size=UDim2.new(0,0)},Blur,0.35)
        t2.Completed:Connect(function() ScreenGui:Destroy() end)
    end)
end
local function SwitchToInfo()
    if isOnInfoPage or isAnimating then return end
    HideAllOverlays();isAnimating=true;isOnInfoPage=true;Footer.Visible=false
    Tw({Position=UDim2.new(-1,0,0,0)},KeyPage,0.45,Enum.EasingStyle.Quint);task.wait(0.45)
    InfoPage.Visible=true;InfoPage.Position=UDim2.new(1,0,0,0)
    Tw({Position=UDim2.new(0,0,0,0)},InfoPage,0.45,Enum.EasingStyle.Quint);task.wait(0.45)
    isAnimating=false
end
local function SwitchToKey()
    if not isOnInfoPage or isAnimating then return end
    HideAllOverlays();isAnimating=true;isOnInfoPage=false
    Tw({Position=UDim2.new(1,0,0,0)},InfoPage,0.45,Enum.EasingStyle.Quint);task.wait(0.45)
    InfoPage.Visible=false;KeyPage.Position=UDim2.new(-1,0,0,0);KeyPage.Visible=true
    Tw({Position=UDim2.new(0,0,0,0)},KeyPage,0.45,Enum.EasingStyle.Quint);task.wait(0.45)
    Footer.Visible=true;isAnimating=false
end

-- ============ VERIFY — с реальной проверкой API ============
local function Verify(key)
    SetStatus("Verifying...",Color3.fromRGB(120,120,120))
    ShowOverlay("loading")
    task.spawn(function()
        local valid, errMsg = VerifyKey(key)
        HideOverlay("loading");task.wait(0.25)
        if valid then
            SaveKey(key)
            if getgenv then getgenv().SCRIPT_KEY=key end
            SetStatus("Verified",Color3.fromRGB(255,255,255))
            ShowOverlay("success")
            Tw({Rotation=405},CheckSvg,0.6,Enum.EasingStyle.Back)
            task.wait(0.8);SuccessSub.Text="Loading module"
            task.wait(0.5);SuccessSub.Text="Injecting"
            task.wait(0.4)
            CloseGUI()
            task.spawn(function()
                pcall(function() loadstring(game:HttpGet(Config.scriptUrl))() end)
            end)
        else
            SetStatus("Invalid key",Color3.fromRGB(255,80,80))
            ErrorTitle.Text="INVALID LICENSE"
            ErrorSub.Text=tostring(errMsg):sub(1,100)
            ShowOverlay("error")
        end
    end)
end

LogoClickBtn.MouseButton1Click:Connect(SwitchToInfo)
LogoClickBtn.MouseEnter:Connect(function() Tw({BackgroundTransparency=0.85},LogoHover,0.2);Tw({BackgroundColor3=Color3.fromRGB(200,200,200)},LogoContainer,0.2) end)
LogoClickBtn.MouseLeave:Connect(function() Tw({BackgroundTransparency=1},LogoHover,0.2);Tw({BackgroundColor3=Color3.fromRGB(255,255,255)},LogoContainer,0.2) end)
BackBtn.MouseButton1Click:Connect(SwitchToKey)
BackBtn.MouseButton1Down:Connect(function(x,y) AddRipple(BackBtn,x-BackBtn.AbsolutePosition.X,y-BackBtn.AbsolutePosition.Y) end)
BackBtn.MouseEnter:Connect(function() Tw({BackgroundColor3=Color3.fromRGB(220,220,220)},BackBtn,0.15) end)
BackBtn.MouseLeave:Connect(function() Tw({BackgroundColor3=Color3.fromRGB(255,255,255)},BackBtn,0.15) end)
InputBox:GetPropertyChangedSignal("Text"):Connect(function() Tw({Color=InputBox.Text~="" and Color3.fromRGB(60,60,60) or Color3.fromRGB(30,30,30)},InputStroke,0.2) end)
InputBox.Focused:Connect(function() Tw({Color=Color3.fromRGB(80,80,80)},InputStroke,0.25);Tw({BackgroundColor3=Color3.fromRGB(6,6,6)},InputWrapper,0.25) end)
InputBox.FocusLost:Connect(function() Tw({Color=Color3.fromRGB(30,30,30)},InputStroke,0.25);Tw({BackgroundColor3=Color3.fromRGB(8,8,8)},InputWrapper,0.25) end)
VerifyBtn.MouseButton1Click:Connect(function()
    local key=InputBox.Text:gsub("%s+","")
    if key=="" then
        SetStatus("Empty field",Color3.fromRGB(255,255,255))
        Tw({Position=UDim2.new(0,-5,0,28)},InputWrapper,0.06) task.wait(0.06)
        Tw({Position=UDim2.new(0,5,0,28)},InputWrapper,0.06) task.wait(0.06)
        Tw({Position=UDim2.new(0,0,0,28)},InputWrapper,0.15);return
    end
    Verify(key)
end)
InputBox.FocusLost:Connect(function(enter) if enter then VerifyBtn.MouseButton1Click:Fire() end end)
VerifyBtn.MouseButton1Down:Connect(function(x,y) AddRipple(VerifyBtn,x-VerifyBtn.AbsolutePosition.X,y-VerifyBtn.AbsolutePosition.Y) end)
VerifyBtn.MouseEnter:Connect(function() Tw({BackgroundColor3=Color3.fromRGB(220,220,220)},VerifyBtn,0.15) end)
VerifyBtn.MouseLeave:Connect(function() Tw({BackgroundColor3=Color3.fromRGB(255,255,255)},VerifyBtn,0.15) end)
RetryBtn.MouseButton1Click:Connect(function() HideOverlay("error");task.wait(0.25);InputBox.Text="";SetStatus("Awaiting input",Color3.fromRGB(80,80,80)) end)
RetryBtn.MouseButton1Down:Connect(function(x,y) AddRipple(RetryBtn,x-RetryBtn.AbsolutePosition.X,y-RetryBtn.AbsolutePosition.Y) end)
RetryBtn.MouseEnter:Connect(function() Tw({BackgroundColor3=Color3.fromRGB(220,220,220)},RetryBtn,0.15) end)
RetryBtn.MouseLeave:Connect(function() Tw({BackgroundColor3=Color3.fromRGB(255,255,255)},RetryBtn,0.15) end)
GetKeyLink.MouseEnter:Connect(function() Tw({TextColor3=Color3.fromRGB(100,100,100)},GetKeyLink,0.15);Tw({Size=UDim2.new(1,0,0,1)},Underline,0.2) end)
GetKeyLink.MouseLeave:Connect(function() Tw({TextColor3=Color3.fromRGB(45,45,45)},GetKeyLink,0.15);Tw({Size=UDim2.new(0,0,0,1)},Underline,0.2) end)
GetKeyLink.MouseButton1Click:Connect(function()
    SetStatus("Opening checkpoint...",Color3.fromRGB(255,255,255))
    pcall(function() setclipboard(Config.keyLink) end)
    pcall(function() os.execute('start "" "'..Config.keyLink..'"') end)
    task.wait(1);SetStatus("Link copied to clipboard",Color3.fromRGB(80,80,80))
    task.wait(2);SetStatus("Awaiting input",Color3.fromRGB(80,80,80))
end)
CloseBtn.MouseEnter:Connect(function() Tw({TextColor3=Color3.fromRGB(255,255,255)},CloseBtn,0.12) end)
CloseBtn.MouseLeave:Connect(function() Tw({TextColor3=Color3.fromRGB(50,50,50)},CloseBtn,0.12) end)
CloseBtn.MouseButton1Click:Connect(CloseGUI)

local function MakeDrag(frame,handle)
    local drag,dragIn,dragStart,startPos=false,nil,nil,nil
    handle.InputBegan:Connect(function(i)
        if i.UserInputType==Enum.UserInputType.MouseButton1 or i.UserInputType==Enum.UserInputType.Touch then
            drag=true;dragStart=i.Position;startPos=frame.Position
            i.Changed:Connect(function() if i.UserInputState==Enum.UserInputState.End then drag=false end end)
        end
    end)
    handle.InputChanged:Connect(function(i) if i.UserInputType==Enum.UserInputType.MouseMovement or i.UserInputType==Enum.UserInputType.Touch then dragIn=i end end)
    UserInputService.InputChanged:Connect(function(i)
        if i==dragIn and drag then local d=i.Position-dragStart;frame.Position=UDim2.new(startPos.X.Scale,startPos.X.Offset+d.X,startPos.Y.Scale,startPos.Y.Offset+d.Y) end
    end)
end
MakeDrag(Card,Header)

local savedKey=LoadKey()
if savedKey~="" then InputBox.Text=savedKey;SetStatus("Key loaded from file",Color3.fromRGB(60,60,60)) end

Card.Size=UDim2.new(0,440,0,0);Card.Position=UDim2.new(0.5,0,0.5,20)
task.wait(0.1)
Tw({Size=UDim2.new(0,440,0,420)},Card,0.7,Enum.EasingStyle.Back)
Tw({Position=UDim2.new(0.5,0,0.5,0)},Card,0.7,Enum.EasingStyle.Back)
Tw({Size=UDim2.new(0,24)},Blur,0.9)
