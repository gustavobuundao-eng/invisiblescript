
-- Bezalel Hub v1.0 — v8 FIXED
-- CORREÇÃO PRINCIPAL: cada aba virou uma local function para evitar "Out of local registers" (limite 200)

if _G.BZSession then _G.BZSession=_G.BZSession+1 else _G.BZSession=1 end
if _G.FLY_LOOP then _G.FLY_LOOP:Disconnect(); _G.FLY_LOOP=nil end

local BALL_IMAGE_ID = "rbxassetid://131443372125030"
pcall(function()
    if getcustomasset then
        BALL_IMAGE_ID = getcustomasset("Bezalel_em_energia_c_smica.png")
    end
end)

-- ============================================================
-- LOADER — transparente + blur
-- ============================================================
local function mostrarLoader(onComplete)
    local cg = gethui and gethui() or game.CoreGui
    local prev = cg:FindFirstChild("BZLoader"); if prev then prev:Destroy() end
    local TS_ = game:GetService("TweenService")
    local Lighting_ = game:GetService("Lighting")

    local loaderBlur = Instance.new("BlurEffect", Lighting_)
    loaderBlur.Size = 20

    local LoadGui = Instance.new("ScreenGui")
    LoadGui.Name = "BZLoader"; LoadGui.ResetOnSpawn = false
    LoadGui.IgnoreGuiInset = true; LoadGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    LoadGui.Parent = cg

    local BG = Instance.new("Frame", LoadGui)
    BG.Size = UDim2.new(1,0,1,0)
    BG.BackgroundColor3 = Color3.fromRGB(8,8,14)
    BG.BackgroundTransparency = 0.22
    BG.BorderSizePixel = 0
    local bgGrad = Instance.new("UIGradient", BG)
    bgGrad.Color = ColorSequence.new{ColorSequenceKeypoint.new(0,Color3.fromRGB(14,8,28)),ColorSequenceKeypoint.new(1,Color3.fromRGB(6,6,14))}
    bgGrad.Rotation = 135

    local logoF = Instance.new("Frame", BG)
    logoF.Size = UDim2.new(0,250,0,78); logoF.AnchorPoint = Vector2.new(0.5,0.5)
    logoF.Position = UDim2.new(0.5,0,0.39,0); logoF.BackgroundColor3 = Color3.fromRGB(130,0,255)
    logoF.BorderSizePixel = 0; logoF.BackgroundTransparency = 1
    local lcr = Instance.new("UICorner",logoF); lcr.CornerRadius = UDim.new(0,16)
    local lg2 = Instance.new("UIGradient",logoF)
    lg2.Color = ColorSequence.new{ColorSequenceKeypoint.new(0,Color3.fromRGB(160,30,255)),ColorSequenceKeypoint.new(1,Color3.fromRGB(70,0,160))}
    lg2.Rotation = 135

    local logoTxt = Instance.new("TextLabel",logoF)
    logoTxt.Size = UDim2.new(1,0,0,40); logoTxt.Position = UDim2.new(0,0,0,10)
    logoTxt.BackgroundTransparency = 1; logoTxt.Text = "Bezalel Hub"
    logoTxt.Font = Enum.Font.GothamBold; logoTxt.TextSize = 24; logoTxt.TextColor3 = Color3.new(1,1,1)
    logoTxt.TextXAlignment = Enum.TextXAlignment.Center; logoTxt.TextTransparency = 1

    local versaoTxt = Instance.new("TextLabel",logoF)
    versaoTxt.Size = UDim2.new(1,0,0,18); versaoTxt.Position = UDim2.new(0,0,0,52)
    versaoTxt.BackgroundTransparency = 1; versaoTxt.Text = "v1.0  —  Iniciando sistema..."
    versaoTxt.Font = Enum.Font.Gotham; versaoTxt.TextSize = 11; versaoTxt.TextColor3 = Color3.fromRGB(200,170,255)
    versaoTxt.TextXAlignment = Enum.TextXAlignment.Center; versaoTxt.TextTransparency = 1

    local statusLbl = Instance.new("TextLabel", BG)
    statusLbl.Size = UDim2.new(0,400,0,22); statusLbl.AnchorPoint = Vector2.new(0.5,0)
    statusLbl.Position = UDim2.new(0.5,0,0.585,0); statusLbl.BackgroundTransparency = 1
    statusLbl.Text = "Aguarde..."; statusLbl.Font = Enum.Font.Gotham
    statusLbl.TextSize = 11; statusLbl.TextColor3 = Color3.fromRGB(140,140,170)
    statusLbl.TextXAlignment = Enum.TextXAlignment.Center; statusLbl.TextTransparency = 1

    local pbTrack = Instance.new("Frame", BG)
    pbTrack.Size = UDim2.new(0,400,0,5); pbTrack.AnchorPoint = Vector2.new(0.5,0)
    pbTrack.Position = UDim2.new(0.5,0,0.638,0); pbTrack.BackgroundColor3 = Color3.fromRGB(22,22,40)
    pbTrack.BorderSizePixel = 0; pbTrack.BackgroundTransparency = 1
    local ptc = Instance.new("UICorner",pbTrack); ptc.CornerRadius = UDim.new(0,99)

    local pbFill = Instance.new("Frame", pbTrack)
    pbFill.Size = UDim2.new(0,0,1,0); pbFill.BackgroundColor3 = Color3.fromRGB(130,0,255)
    pbFill.BorderSizePixel = 0
    local pfc = Instance.new("UICorner",pbFill); pfc.CornerRadius = UDim.new(0,99)
    local pgGrad = Instance.new("UIGradient",pbFill)
    pgGrad.Color = ColorSequence.new{ColorSequenceKeypoint.new(0,Color3.fromRGB(170,40,255)),ColorSequenceKeypoint.new(1,Color3.fromRGB(90,0,200))}

    local checks = {
        {0.14,"Verificando workspace..."},
        {0.28,"Conectando servico de jogadores..."},
        {0.44,"Carregando servicos do jogo..."},
        {0.60,"Verificando iluminacao..."},
        {0.74,"Preparando interface visual..."},
        {0.89,"Carregando recursos..."},
        {1.00,"✔  Hub pronto!"},
    }

    task.spawn(function()
        task.wait(0.2)
        TS_:Create(logoF,TweenInfo.new(0.4,Enum.EasingStyle.Quad),{BackgroundTransparency=0}):Play()
        TS_:Create(logoTxt,TweenInfo.new(0.4),{TextTransparency=0}):Play()
        TS_:Create(versaoTxt,TweenInfo.new(0.4),{TextTransparency=0}):Play()
        task.wait(0.3)
        TS_:Create(statusLbl,TweenInfo.new(0.3),{TextTransparency=0}):Play()
        TS_:Create(pbTrack,TweenInfo.new(0.3),{BackgroundTransparency=0}):Play()
        task.wait(0.3)
        pcall(function() if not game:IsLoaded() then game.Loaded:Wait() end end)
        for _,c in ipairs(checks) do
            TS_:Create(pbFill,TweenInfo.new(0.3,Enum.EasingStyle.Quad),{Size=UDim2.new(c[1],0,1,0)}):Play()
            statusLbl.Text = c[2]; task.wait(0.38)
        end
        versaoTxt.Text = "✔  Iniciando hub..."; task.wait(0.5)
        TS_:Create(BG,TweenInfo.new(0.55,Enum.EasingStyle.Sine),{BackgroundTransparency=1}):Play()
        TS_:Create(loaderBlur,TweenInfo.new(0.55,Enum.EasingStyle.Sine),{Size=0}):Play()
        for _,v in pairs(BG:GetDescendants()) do
            if v:IsA("TextLabel") then TS_:Create(v,TweenInfo.new(0.4),{TextTransparency=1}):Play()
            elseif v:IsA("Frame") then TS_:Create(v,TweenInfo.new(0.4),{BackgroundTransparency=1}):Play() end
        end
        task.wait(0.65); loaderBlur:Destroy(); LoadGui:Destroy(); onComplete()
    end)
end

-- ============================================================
-- BEZALEL_MAIN
-- ============================================================
local function BEZALEL_MAIN()
local BZ_SID=_G.BZSession

for _,n in pairs({"BezalelHub","TLGui"}) do
    local v=game.CoreGui:FindFirstChild(n); if v then v:Destroy() end
    local pg=game:GetService("Players").LocalPlayer:FindFirstChild("PlayerGui")
    if pg then local v2=pg:FindFirstChild(n); if v2 then v2:Destroy() end end
end
do local o=workspace:FindFirstChild("_BZv10_ESP"); if o then o:Destroy() end end
local c2=workspace:FindFirstChild("_BZinvischair"); if c2 then c2:Destroy() end

local Players=game:GetService("Players")
local RunService=game:GetService("RunService")
local UserInputService=game:GetService("UserInputService")
local TweenService=game:GetService("TweenService")
local Lighting=game:GetService("Lighting")
local SoundService=game:GetService("SoundService")
local CAS=game:GetService("ContextActionService")
local Debris=game:GetService("Debris")
local MarketplaceService=game:GetService("MarketplaceService")
local TeleportService=game:GetService("TeleportService")
local HttpService=game:GetService("HttpService")
local LocalPlayer=Players.LocalPlayer
local Camera=workspace.CurrentCamera

local AIM_BONE="Head"; local AIM_VEL_PROJ=300; local AIM_MAX_T=0.40
local AIM_PREDICT={{maxDist=40,mult=0.30},{maxDist=100,mult=0.55},{maxDist=200,mult=0.75},{maxDist=math.huge,mult=0.50}}
local function aimGetMult(dist) for _,b in ipairs(AIM_PREDICT) do if dist<b.maxDist then return b.mult end end; return AIM_PREDICT[#AIM_PREDICT].mult end
local function aimGetPart(char) return char:FindFirstChild(AIM_BONE) or char:FindFirstChild("HumanoidRootPart") end
local aimLockedTarget = nil
local aimLastPos = nil

local SIDEBAR_W=160
local flyAtivo=false; local invisAtivo=false
local superPuloAtivo=false; local noclipAtivo=false; local noclipConn=nil
local clickTPAtivo=false; local fpsAtivo=false; local fpsConn=nil
local espAtivo=true; local espNome=true; local espHP=true
local espHighlight=false; local espHLColor=Color3.fromRGB(130,0,255)
local espHLFillT=0.82; local espDist=false; local espDistColor=Color3.fromRGB(255,255,255)
local aimAtivo=false
local tlAtivo=true; local tlModoAtivo=false; local tlHovered=nil; local tlHLs={}; local tlRmbHeld=false
local maxTlDist = 80
local janelaTravada=false; local hubVisible=false; local freecamAtivo=false
local flySpeed=150; local flyBoost=400
local jumpHeight=120; local aimFOV=8; local aimSmooth=0; local flyLoop=nil
local hubW,hubH=600,420
local spectateTarget=nil; local spectateConn=nil
local spectOrbitX,spectOrbitY,spectOrbitDist=0,0,10
local spectSavedWS=16; local spectSavedJP=50
local useBall=false

local flingAtivo=false
local flingRadius=20
local flingOldPos=nil
local flingFPDH=workspace.FallenPartsDestroyHeight

local kbGated={fly=false,invis=false,jump=false,freecam=false,noclip=false,fling=false}
local KB={hub=Enum.KeyCode.Home,fly=Enum.KeyCode.Unknown,invis=Enum.KeyCode.Unknown,
    jump=Enum.KeyCode.Unknown,vision=Enum.KeyCode.Unknown,
    tl=Enum.KeyCode.Unknown,freecam=Enum.KeyCode.Unknown,noclip=Enum.KeyCode.Unknown,
    fling=Enum.KeyCode.Unknown}

local kbEscutando=false; local kbCb=nil; local activeDrag=nil
local tpSaves={}; local allCards={}; local currentTab=""
local mostrarHub,esconderHub,toggleHub,trocarAba
local toggleFreecam; local fcTgtFOV=Camera.FieldOfView
local espCache={}; local espHLCache={}
local updateHLColor,updateEspHL,registerESP
local allToggleSetters={}
local espFolder=Instance.new("Folder",workspace); espFolder.Name="_BZv10_ESP"

local HC={
    Accent=Color3.fromRGB(130,0,255), AccentDark=Color3.fromRGB(90,0,180),
    Background=Color3.fromRGB(14,14,21), Surface=Color3.fromRGB(22,22,34),
    Surface2=Color3.fromRGB(30,30,46), Border=Color3.fromRGB(45,45,65),
    Text=Color3.fromRGB(240,240,255), TextMuted=Color3.fromRGB(140,140,170),
    Success=Color3.fromRGB(80,220,120), Danger=Color3.fromRGB(255,75,75),
    Info=Color3.fromRGB(80,160,255), Green=Color3.fromRGB(55,195,75),
}

local SFX
do
    local function mkS(id,vol)
        local s=Instance.new("Sound"); s.SoundId="rbxassetid://"..id
        s.Volume=vol or 0.4; s.RollOffMaxDistance=0; s.Parent=SoundService; return s
    end
    SFX={click=mkS("6895079853",0.25),toggle=mkS("3200000779",0.40),
         notif=mkS("4590657391",0.30),open=mkS("4239833267",0.50),
         vision=mkS("75158864675813",0.55)}
end
local function pC() pcall(function() SFX.click:Play() end) end
local function pT() pcall(function() SFX.toggle:Play() end) end
local function pN() pcall(function() SFX.notif:Play() end) end
local function pO() pcall(function() SFX.open:Play() end) end

local function getHRP()
    return (LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()):WaitForChild("HumanoidRootPart",5)
end
local function kbNome(kc)
    if kc==Enum.KeyCode.Unknown then return "-- vazio --" end
    return tostring(kc):gsub("Enum%.KeyCode%.","")
end
local function tw(o,p,t,s,d)
    return TweenService:Create(o,TweenInfo.new(t or .18,s or Enum.EasingStyle.Quad,d or Enum.EasingDirection.Out),p)
end
local function corner(p,r) local c=Instance.new("UICorner",p); c.CornerRadius=UDim.new(0,r or 8); return c end
local function pad(p,l,r,t,b)
    local x=Instance.new("UIPadding",p)
    x.PaddingLeft=UDim.new(0,l or 0); x.PaddingRight=UDim.new(0,r or l or 0)
    x.PaddingTop=UDim.new(0,t or l or 0); x.PaddingBottom=UDim.new(0,b or l or 0)
end
local function lbl(parent,txt,size,font,color,bgt)
    local l=Instance.new("TextLabel",parent)
    l.Text=txt; l.TextSize=size or 13; l.Font=font or Enum.Font.Gotham
    l.TextColor3=color or HC.Text; l.BackgroundTransparency=bgt~=nil and bgt or 1
    l.BorderSizePixel=0; return l
end
local function stroke(obj,color,t)
    local s=Instance.new("UIStroke",obj); s.Color=color or HC.Border; s.Thickness=1; s.Transparency=t or 0.5; return s
end

-- ============================================================
-- CAMERA LIVRE
-- ============================================================
do
    local fcConn=nil
    local fcAlteredObjs={}; local fcNomeConns={}
    local fcMiraObjs={}; local fcMiraConn=nil
    local fcCamPos,fcTargetPos=nil,nil
    local fcRotX,fcRotY,fcTgtRotX,fcTgtRotY=0,0,0,0
    local fcCurFOV=Camera.FieldOfView
    local fcFreezeConn=nil; local fcFrozenCF=nil
    local fcScrollConn=nil

    local function fcBlockAll()
        CAS:BindActionAtPriority("_BZ_FCBlockAll",function(name,state,obj)
            if state==Enum.UserInputState.Begin then
                if obj.KeyCode==KB.freecam and kbGated.freecam then
                    task.defer(toggleFreecam)
                    return Enum.ContextActionResult.Sink
                end
            end
            return Enum.ContextActionResult.Sink
        end,false,Enum.ContextActionPriority.High.Value+100,table.unpack(Enum.KeyCode:GetEnumItems()))
        CAS:BindActionAtPriority("_BZ_FCBlockMouse",function(name,state,obj)
            return Enum.ContextActionResult.Sink
        end,false,Enum.ContextActionPriority.High.Value+100,
            Enum.UserInputType.MouseButton1,
            Enum.UserInputType.MouseButton2,
            Enum.UserInputType.MouseButton3)
    end
    local function fcUnblockAll()
        CAS:UnbindAction("_BZ_FCBlockAll")
        CAS:UnbindAction("_BZ_FCBlockMouse")
    end

    local function fcFreeze()
        local char=LocalPlayer.Character; if not char then return end
        local hum=char:FindFirstChildOfClass("Humanoid")
        local root=char:FindFirstChild("HumanoidRootPart")
        if root then fcFrozenCF=root.CFrame; root.Anchored=true end
        if hum then hum.AutoRotate=false; hum:ChangeState(Enum.HumanoidStateType.Physics) end
        if fcFreezeConn then fcFreezeConn:Disconnect() end
        fcFreezeConn=RunService.Heartbeat:Connect(function()
            if _G.BZSession~=BZ_SID then fcFreezeConn:Disconnect(); return end
            if not freecamAtivo then return end
            local r=LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
            if r and fcFrozenCF then r.CFrame=fcFrozenCF end
        end)
    end
    local function fcUnfreeze()
        if fcFreezeConn then fcFreezeConn:Disconnect(); fcFreezeConn=nil end; fcFrozenCF=nil
        local char=LocalPlayer.Character; if not char then return end
        local hum=char:FindFirstChildOfClass("Humanoid")
        local root=char:FindFirstChild("HumanoidRootPart")
        if root then root.Anchored=false end
        if hum then hum.AutoRotate=true; hum:ChangeState(Enum.HumanoidStateType.GettingUp) end
    end

    local function fcProcessDesc(v)
        if not v or fcAlteredObjs[v] then return end
        local p=v.Parent; local inESP=false
        while p do if p==espFolder then inESP=true; break end; p=p.Parent end
        if inESP then return end
        if v:IsA("BillboardGui") then fcAlteredObjs[v]={type="Enabled",value=v.Enabled}; v.Enabled=false
        elseif v:IsA("TextLabel") or v:IsA("ImageLabel") then fcAlteredObjs[v]={type="Visible",value=v.Visible}; v.Visible=false end
    end
    local function fcApplyToChar(char)
        for _,v in pairs(char:GetDescendants()) do fcProcessDesc(v) end
        local c=char.DescendantAdded:Connect(function(v) if not freecamAtivo then return end; task.wait(); fcProcessDesc(v) end)
        table.insert(fcNomeConns,c)
    end
    local function fcEsconderNomes()
        for _,p in ipairs(Players:GetPlayers()) do
            if p.Character then fcApplyToChar(p.Character) end
            local c=p.CharacterAdded:Connect(function(char)
                if not freecamAtivo then return end; task.wait(0.3); fcApplyToChar(char)
            end)
            table.insert(fcNomeConns,c)
        end
    end
    local function fcRestaurarNomes()
        for _,c in ipairs(fcNomeConns) do c:Disconnect() end; fcNomeConns={}
        for obj,data in pairs(fcAlteredObjs) do
            if obj and obj.Parent then
                if data.type=="Enabled" then obj.Enabled=data.value
                elseif data.type=="Visible" then obj.Visible=data.value end
            end
        end; fcAlteredObjs={}
    end
    local function fcCheckMira(obj)
        if not freecamAtivo or fcMiraObjs[obj] then return end
        if obj:IsA("ImageLabel") or obj:IsA("Frame") then
            if obj.Size.X.Offset>=20 and obj.Size.X.Offset<=200 and obj.BackgroundTransparency==1 then
                fcMiraObjs[obj]=true; obj.Visible=false
            end
        end
    end
    local function fcEsconderMira()
        local gui=LocalPlayer:FindFirstChild("PlayerGui"); if not gui then return end
        for _,v in pairs(gui:GetDescendants()) do fcCheckMira(v) end
        if fcMiraConn then fcMiraConn:Disconnect() end
        fcMiraConn=gui.DescendantAdded:Connect(function(v) task.wait(); if freecamAtivo then fcCheckMira(v) end end)
    end
    local function fcRestaurarMira()
        if fcMiraConn then fcMiraConn:Disconnect(); fcMiraConn=nil end
        for obj in pairs(fcMiraObjs) do if obj and obj.Parent then obj.Visible=true end end; fcMiraObjs={}
    end

    toggleFreecam=function()
        freecamAtivo=not freecamAtivo
        if freecamAtivo then
            fcBlockAll(); fcFreeze(); fcEsconderNomes(); fcEsconderMira()
            fcCamPos=Camera.CFrame.Position; fcTargetPos=fcCamPos
            local x,y=Camera.CFrame:ToOrientation()
            fcRotX,fcRotY=x,y; fcTgtRotX,fcTgtRotY=x,y
            fcCurFOV=Camera.FieldOfView; fcTgtFOV=fcCurFOV
            Camera.CameraType=Enum.CameraType.Scriptable

            fcScrollConn=UserInputService.InputChanged:Connect(function(input)
                if not freecamAtivo then return end
                if input.UserInputType==Enum.UserInputType.MouseWheel then
                    fcTgtFOV=math.clamp(fcTgtFOV - input.Position.Z*6, 10, 110)
                end
            end)

            fcConn=RunService.RenderStepped:Connect(function()
                if _G.BZSession~=BZ_SID then fcConn:Disconnect(); return end
                local spd
                if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then
                    spd = 0.08
                elseif UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then
                    spd = 3
                else
                    spd = 0.5
                end
                local mv=Vector3.zero
                if UserInputService:IsKeyDown(Enum.KeyCode.W) then mv=mv+Vector3.new(0,0,-1) end
                if UserInputService:IsKeyDown(Enum.KeyCode.S) then mv=mv+Vector3.new(0,0,1) end
                if UserInputService:IsKeyDown(Enum.KeyCode.A) then mv=mv+Vector3.new(-1,0,0) end
                if UserInputService:IsKeyDown(Enum.KeyCode.D) then mv=mv+Vector3.new(1,0,0) end
                if UserInputService:IsKeyDown(Enum.KeyCode.E) then mv=mv+Vector3.new(0,1,0) end
                if UserInputService:IsKeyDown(Enum.KeyCode.Q) then mv=mv+Vector3.new(0,-1,0) end
                if mv.Magnitude>0 then mv=mv.Unit end
                local delta=UserInputService:GetMouseDelta()
                fcTgtRotY=fcTgtRotY-delta.X*0.003; fcTgtRotX=fcTgtRotX-delta.Y*0.003
                fcTgtRotX=math.clamp(fcTgtRotX,-1.5,1.5)
                fcRotX=fcRotX+(fcTgtRotX-fcRotX)*0.055; fcRotY=fcRotY+(fcTgtRotY-fcRotY)*0.055
                local rot=CFrame.Angles(0,fcRotY,0)*CFrame.Angles(fcRotX,0,0)
                fcTargetPos=fcTargetPos+rot:VectorToWorldSpace(mv)*spd
                fcCamPos=fcCamPos+(fcTargetPos-fcCamPos)*0.07
                fcCurFOV=fcCurFOV+(fcTgtFOV-fcCurFOV)*0.38
                Camera.FieldOfView=fcCurFOV; Camera.CFrame=CFrame.new(fcCamPos)*rot
            end)
        else
            if fcConn then fcConn:Disconnect(); fcConn=nil end
            if fcScrollConn then fcScrollConn:Disconnect(); fcScrollConn=nil end
            Camera.CameraType=Enum.CameraType.Custom; Camera.FieldOfView=70
            fcUnblockAll(); fcUnfreeze(); fcRestaurarNomes(); fcRestaurarMira()
        end
    end
end

-- ============================================================
-- ESP
-- ============================================================
do
    updateHLColor=function()
        for _,h in pairs(espHLCache) do
            if h and h.Parent then h.OutlineColor=espHLColor; h.FillColor=espHLColor; h.FillTransparency=espHLFillT end
        end
    end
    updateEspHL=function()
        if espHighlight then
            for _,p in ipairs(Players:GetPlayers()) do
                if p~=LocalPlayer and p.Character and not espHLCache[p] then
                    local h=Instance.new("Highlight",p.Character)
                    h.OutlineColor=espHLColor; h.FillColor=espHLColor; h.FillTransparency=espHLFillT
                    h.OutlineTransparency=0; h.DepthMode=Enum.HighlightDepthMode.AlwaysOnTop; espHLCache[p]=h
                end
            end
        else
            for p,h in pairs(espHLCache) do if h then pcall(function() h:Destroy() end) end end; espHLCache={}
        end
    end
    local function attachESP(player,char)
        local d=espCache[player]; if not d then return end
        local root=char:WaitForChild("HumanoidRootPart",5); if not root then return end
        d.gui.Adornee=root
        local oldH=espHLCache[player]; if oldH then pcall(function() oldH:Destroy() end); espHLCache[player]=nil end
        if espHighlight then
            local h=Instance.new("Highlight",char)
            h.OutlineColor=espHLColor; h.FillColor=espHLColor; h.FillTransparency=espHLFillT
            h.OutlineTransparency=0; h.DepthMode=Enum.HighlightDepthMode.AlwaysOnTop; espHLCache[player]=h
        end
    end
    registerESP=function(player)
        if player==LocalPlayer or espCache[player] then return end
        local gui=Instance.new("BillboardGui")
        gui.Name="ESP_"..player.Name; gui.AlwaysOnTop=true
        gui.Size=UDim2.new(0,140,0,58); gui.StudsOffset=Vector3.new(0,2.8,0); gui.LightInfluence=0; gui.Parent=espFolder
        local nLbl=Instance.new("TextLabel",gui); nLbl.Size=UDim2.new(1,0,0,18); nLbl.BackgroundTransparency=1
        nLbl.TextSize=14; nLbl.Font=Enum.Font.GothamBold; nLbl.Text=player.Name
        nLbl.TextStrokeTransparency=0.35; nLbl.TextStrokeColor3=Color3.new(0,0,0)
        local hLbl=Instance.new("TextLabel",gui); hLbl.Size=UDim2.new(1,0,0,16); hLbl.Position=UDim2.new(0,0,0,19)
        hLbl.BackgroundTransparency=1; hLbl.TextSize=13; hLbl.Font=Enum.Font.Gotham; hLbl.Text="0/0"
        hLbl.TextStrokeTransparency=0.4; hLbl.TextStrokeColor3=Color3.new(0,0,0)
        local dLbl=Instance.new("TextLabel",gui); dLbl.Size=UDim2.new(1,0,0,14); dLbl.Position=UDim2.new(0,0,0,37)
        dLbl.BackgroundTransparency=1; dLbl.TextSize=11; dLbl.Font=Enum.Font.Gotham; dLbl.TextColor3=espDistColor
        dLbl.TextStrokeTransparency=0.5; dLbl.TextStrokeColor3=Color3.new(0,0,0)
        espCache[player]={gui=gui,nLbl=nLbl,hLbl=hLbl,dLbl=dLbl}
        if player.Character then task.spawn(attachESP,player,player.Character) end
        player.CharacterAdded:Connect(function(c) task.wait(0.5); attachESP(player,c) end)
    end
    for _,p in ipairs(Players:GetPlayers()) do registerESP(p) end
    Players.PlayerAdded:Connect(registerESP)
    Players.PlayerRemoving:Connect(function(p)
        local d=espCache[p]; if d and d.gui then pcall(function() d.gui:Destroy() end); espCache[p]=nil end
        local h=espHLCache[p]; if h then pcall(function() h:Destroy() end); espHLCache[p]=nil end
    end)
    RunService.RenderStepped:Connect(function()
        if _G.BZSession~=BZ_SID then return end
        local myRoot=LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
        for player,data in pairs(espCache) do
            local char=player.Character; local hum=char and char:FindFirstChildOfClass("Humanoid")
            local root=char and char:FindFirstChild("HumanoidRootPart")
            if not espAtivo or not hum or not root then data.gui.Enabled=false; continue end
            local hp=math.floor(hum.Health); local maxHp=math.max(math.floor(hum.MaxHealth),1)
            local color
            do
                local r=math.clamp(hp/maxHp,0,1)
                if r>=0.5 then color=Color3.new(1-(r-.5)/.5,1,0)
                elseif r>=0.25 then local t=(r-.25)/.25; color=Color3.new(1,t*.65+(1-t)*.45,0)
                else color=Color3.new(1,(r/.25)*.45,0) end
            end
            data.nLbl.Visible=espNome; data.hLbl.Visible=espHP; data.dLbl.Visible=espDist
            if espNome then data.nLbl.Text=player.Name; data.nLbl.TextColor3=color end
            if espHP then data.hLbl.Text=hp.."/"..maxHp; data.hLbl.TextColor3=color end
            if espDist and myRoot then
                data.dLbl.Text=math.floor((root.Position-myRoot.Position).Magnitude).."m"
                data.dLbl.TextColor3=espDistColor
            end
            data.gui.Enabled=true
        end
    end)
end

-- ============================================================
-- FLY
-- ============================================================
local function clearFlyForces()
    local ok,root=pcall(getHRP); if not ok then return end
    for _,v in pairs(root:GetChildren()) do if v.Name=="FlyForce" or v.Name=="FlyGyro" then v:Destroy() end end
end
local function startFly()
    clearFlyForces(); local root=getHRP()
    local gyro=Instance.new("BodyGyro",root); gyro.Name="FlyGyro"; gyro.MaxTorque=Vector3.new(9e9,9e9,9e9); gyro.P=9e4
    local vel=Instance.new("BodyVelocity",root); vel.Name="FlyForce"; vel.MaxForce=Vector3.new(9e9,9e9,9e9)
    if flyLoop then flyLoop:Disconnect() end
    flyLoop=RunService.RenderStepped:Connect(function()
        if _G.BZSession~=BZ_SID then flyLoop:Disconnect(); return end
        if not flyAtivo then return end
        local cam=workspace.CurrentCamera; local m=Vector3.zero
        if UserInputService:IsKeyDown(Enum.KeyCode.W) then m=m+cam.CFrame.LookVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.S) then m=m-cam.CFrame.LookVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.A) then m=m-cam.CFrame.RightVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.D) then m=m+cam.CFrame.RightVector end
        local spd=UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) and flyBoost or flySpeed
        vel.Velocity=(m.Magnitude>0 and m.Unit or m)*spd; gyro.CFrame=cam.CFrame
    end); _G.FLY_LOOP=flyLoop
end
local function stopFly()
    clearFlyForces(); if flyLoop then flyLoop:Disconnect(); flyLoop=nil end; _G.FLY_LOOP=nil
end

local function toggleInvis()
    invisAtivo=not invisAtivo
    local char=LocalPlayer.Character
    if not char then invisAtivo=not invisAtivo; return end
    if invisAtivo then
        local hrp=char:FindFirstChild("HumanoidRootPart")
        if not hrp then invisAtivo=false; return end
        local savedPos=hrp.CFrame
        char:MoveTo(Vector3.new(-25.95,84,3537.55))
        task.wait(0.15)
        local Seat=Instance.new("Seat",workspace)
        Seat.Name="_BZinvischair"; Seat.Anchored=false; Seat.CanCollide=false; Seat.Transparency=1
        Seat.Position=Vector3.new(-25.95,84,3537.55)
        local Weld=Instance.new("Weld",Seat); Weld.Part0=Seat
        Weld.Part1=char:FindFirstChild("Torso") or char:FindFirstChild("UpperTorso")
        task.wait(); Seat.CFrame=savedPos
    else
        local chair=workspace:FindFirstChild("_BZinvischair"); if chair then chair:Destroy() end
    end
end

local function connectSuperJump(char)
    local hum=char and char:FindFirstChildOfClass("Humanoid"); if not hum then return end
    hum.Jumping:Connect(function(isJumping)
        if not isJumping or not superPuloAtivo then return end
        local hrp=char:FindFirstChild("HumanoidRootPart"); if not hrp then return end
        task.wait(0.01)
        local bv=Instance.new("BodyVelocity",hrp); bv.Name="SuperJumpBV"
        bv.Velocity=Vector3.new(0,jumpHeight,0); bv.MaxForce=Vector3.new(0,math.huge,0); bv.P=math.huge
        Debris:AddItem(bv,0.15)
    end)
end
task.spawn(function()
    if LocalPlayer.Character then connectSuperJump(LocalPlayer.Character) end
    LocalPlayer.CharacterAdded:Connect(function(c) task.wait(0.5); connectSuperJump(c) end)
end)

local function toggleNoclip()
    noclipAtivo=not noclipAtivo
    if noclipAtivo then
        noclipConn=RunService.Stepped:Connect(function()
            if _G.BZSession~=BZ_SID then noclipConn:Disconnect(); return end
            local char=LocalPlayer.Character; if not char then return end
            for _,v in pairs(char:GetDescendants()) do if v:IsA("BasePart") then v.CanCollide=false end end
        end)
    else
        if noclipConn then noclipConn:Disconnect(); noclipConn=nil end
        local char=LocalPlayer.Character
        if char then for _,v in pairs(char:GetDescendants()) do if v:IsA("BasePart") then v.CanCollide=true end end end
    end
end

local colorFx=Lighting:FindFirstChild("VisionEffect") or Instance.new("ColorCorrectionEffect",Lighting)
colorFx.Name="VisionEffect"
local visionHLs={}; local visionBusy=false
local function ativarVisao()
    if visionBusy then return end; visionBusy=true
    pcall(function() SFX.vision:Play() end)
    for _,p in pairs(Players:GetPlayers()) do
        if p~=LocalPlayer and p.Character then
            local h=Instance.new("Highlight",p.Character)
            h.FillColor=Color3.fromRGB(255,0,0); h.FillTransparency=0.3; h.OutlineTransparency=0
            h.DepthMode=Enum.HighlightDepthMode.AlwaysOnTop; table.insert(visionHLs,h)
        end
    end
    tw(colorFx,{Brightness=-0.3,Contrast=0.5,Saturation=-1},0.5):Play(); task.wait(5)
    tw(colorFx,{Brightness=0,Contrast=0,Saturation=0},0.5):Play()
    for _,h in pairs(visionHLs) do if h then pcall(function() h:Destroy() end) end end
    visionHLs={}; visionBusy=false
end

-- ============================================================
-- PROXIMITY FLING
-- ============================================================
local function getFlingNearby()
    local list={}
    local myChar=LocalPlayer.Character; if not myChar then return list end
    local myRoot=myChar:FindFirstChild("HumanoidRootPart"); if not myRoot then return list end
    for _,p in ipairs(Players:GetPlayers()) do
        if p~=LocalPlayer and p.Character then
            local r=p.Character:FindFirstChild("HumanoidRootPart")
            if r and (r.Position-myRoot.Position).Magnitude<=flingRadius then
                table.insert(list,p)
            end
        end
    end
    return list
end

local function doFling(target)
    local char=LocalPlayer.Character
    local hum=char and char:FindFirstChildOfClass("Humanoid")
    local root=hum and hum.RootPart
    local tChar=target.Character
    if not (char and hum and root and tChar) then return end
    local tHum=tChar:FindFirstChildOfClass("Humanoid")
    local tRoot=tHum and tHum.RootPart
    local tHead=tChar:FindFirstChild("Head")
    local acc=tChar:FindFirstChildOfClass("Accessory")
    local handle=acc and acc:FindFirstChild("Handle")
    if not tChar:FindFirstChildWhichIsA("BasePart") then return end
    if root.Velocity.Magnitude<50 then flingOldPos=root.CFrame end
    if tHead then workspace.CurrentCamera.CameraSubject=tHead
    elseif handle then workspace.CurrentCamera.CameraSubject=handle
    elseif tHum then workspace.CurrentCamera.CameraSubject=tHum end
    local function fpos(part,pos,ang)
        root.CFrame=CFrame.new(part.Position)*pos*ang
        char:SetPrimaryPartCFrame(CFrame.new(part.Position)*pos*ang)
        root.Velocity=Vector3.new(9e7,9e7*10,9e7)
        root.RotVelocity=Vector3.new(9e8,9e8,9e8)
    end
    local function sfpart(part)
        local t=tick(); local angle=0
        repeat
            if root and tHum then
                if part.Velocity.Magnitude<50 then
                    angle=angle+100
                    fpos(part,CFrame.new(0,1.5,0)+tHum.MoveDirection*part.Velocity.Magnitude/1.25,CFrame.Angles(math.rad(angle),0,0)) task.wait()
                    fpos(part,CFrame.new(0,-1.5,0)+tHum.MoveDirection*part.Velocity.Magnitude/1.25,CFrame.Angles(math.rad(angle),0,0)) task.wait()
                    fpos(part,CFrame.new(0,1.5,0)+tHum.MoveDirection,CFrame.Angles(math.rad(angle),0,0)) task.wait()
                    fpos(part,CFrame.new(0,-1.5,0)+tHum.MoveDirection,CFrame.Angles(math.rad(angle),0,0)) task.wait()
                else
                    fpos(part,CFrame.new(0,1.5,tHum.WalkSpeed),CFrame.Angles(math.rad(90),0,0)) task.wait()
                    fpos(part,CFrame.new(0,-1.5,-tHum.WalkSpeed),CFrame.Angles(0,0,0)) task.wait()
                    fpos(part,CFrame.new(0,-1.5,0),CFrame.Angles(math.rad(90),0,0)) task.wait()
                    fpos(part,CFrame.new(0,-1.5,0),CFrame.Angles(0,0,0)) task.wait()
                end
            end
        until t+2<tick() or not flingAtivo
    end
    workspace.FallenPartsDestroyHeight=0/0
    local bv=Instance.new("BodyVelocity"); bv.Velocity=Vector3.new(0,0,0)
    bv.MaxForce=Vector3.new(9e9,9e9,9e9); bv.Parent=root
    hum:SetStateEnabled(Enum.HumanoidStateType.Seated,false)
    if tRoot then sfpart(tRoot) elseif tHead then sfpart(tHead) elseif handle then sfpart(handle) end
    bv:Destroy()
    hum:SetStateEnabled(Enum.HumanoidStateType.Seated,true)
    workspace.CurrentCamera.CameraSubject=hum
    if flingOldPos then
        repeat
            root.CFrame=flingOldPos*CFrame.new(0,.5,0)
            char:SetPrimaryPartCFrame(flingOldPos*CFrame.new(0,.5,0))
            hum:ChangeState("GettingUp")
            for _,p in pairs(char:GetChildren()) do
                if p:IsA("BasePart") then p.Velocity=Vector3.new(); p.RotVelocity=Vector3.new() end
            end
            task.wait()
        until (root.Position-flingOldPos.p).Magnitude<25
        workspace.FallenPartsDestroyHeight=flingFPDH
    end
end

task.spawn(function()
    while _G.BZSession==BZ_SID do
        if flingAtivo then
            local nearby=getFlingNearby()
            for _,p in ipairs(nearby) do
                if not flingAtivo then break end
                pcall(doFling,p)
                task.wait(0.1)
            end
        end
        task.wait(0.5)
    end
end)

-- ============================================================
-- AIMBOT LOOP
-- ============================================================
RunService.RenderStepped:Connect(function()
    if _G.BZSession~=BZ_SID then return end
    if not aimAtivo then aimLockedTarget=nil; aimLastPos=nil; return end
    if not UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton2) then
        aimLockedTarget=nil; aimLastPos=nil; return
    end
    if not aimLockedTarget then return end
    local char=aimLockedTarget.Character
    local hum=char and char:FindFirstChildOfClass("Humanoid")
    if not hum or hum.Health<=0 or not aimLockedTarget.Parent then
        aimLockedTarget=nil; aimLastPos=nil; return
    end
    local aimPart=aimGetPart(char); if not aimPart then aimLockedTarget=nil; aimLastPos=nil; return end
    if aimLastPos then
        local moved=(aimPart.Position-aimLastPos).Magnitude
        if moved>60 then aimLockedTarget=nil; aimLastPos=nil; return end
    end
    aimLastPos=aimPart.Position
    local hrp=char:FindFirstChild("HumanoidRootPart"); if not hrp then return end
    local pos=aimPart.Position
    local vel=hrp.AssemblyLinearVelocity
    local dist=(pos-Camera.CFrame.Position).Magnitude
    local tempo=math.min((dist/AIM_VEL_PROJ)*aimGetMult(dist),AIM_MAX_T)
    local tCF=CFrame.new(Camera.CFrame.Position,pos+vel*tempo)
    if aimSmooth<=0 then Camera.CFrame=tCF else Camera.CFrame=Camera.CFrame:Lerp(tCF,0.3/aimSmooth) end
end)

-- ============================================================
-- SPECTATE
-- ============================================================
local function startSpectate(player)
    spectateTarget=player
    Camera.CameraType=Enum.CameraType.Scriptable
    local _,ry,_=Camera.CFrame:ToOrientation()
    spectOrbitY=ry; spectOrbitX=0; spectOrbitDist=10
    local myChar=LocalPlayer.Character
    if myChar then
        local hrp=myChar:FindFirstChild("HumanoidRootPart")
        local hum=myChar:FindFirstChildOfClass("Humanoid")
        if hrp then hrp.Anchored=true end
        if hum then
            spectSavedWS=hum.WalkSpeed; spectSavedJP=hum.JumpPower
            hum.WalkSpeed=0; hum.JumpPower=0; hum.AutoRotate=false
            hum:ChangeState(Enum.HumanoidStateType.Physics)
        end
    end
    CAS:BindActionAtPriority("_BZ_SpectBlock",function()
        return Enum.ContextActionResult.Sink
    end,false,Enum.ContextActionPriority.High.Value+150,table.unpack(Enum.KeyCode:GetEnumItems()))
    if spectateConn then spectateConn:Disconnect() end
    spectateConn=RunService.RenderStepped:Connect(function()
        if _G.BZSession~=BZ_SID then spectateConn:Disconnect(); return end
        if not spectateTarget or spectateTarget~=player then return end
        local tChar=player.Character; if not tChar then return end
        local hrp=tChar:FindFirstChild("HumanoidRootPart"); if not hrp then return end
        local targetPos=hrp.Position+Vector3.new(0,2,0)
        local rot=CFrame.Angles(0,spectOrbitY,0)*CFrame.Angles(spectOrbitX,0,0)
        local camPos=targetPos+rot:VectorToWorldSpace(Vector3.new(0,0,spectOrbitDist))
        Camera.CFrame=CFrame.new(camPos,targetPos)
    end)
end
local function stopSpectate()
    spectateTarget=nil
    if spectateConn then spectateConn:Disconnect(); spectateConn=nil end
    CAS:UnbindAction("_BZ_SpectBlock")
    Camera.CameraType=Enum.CameraType.Custom; Camera.FieldOfView=70
    local myChar=LocalPlayer.Character
    if myChar then
        local hrp=myChar:FindFirstChild("HumanoidRootPart")
        local hum=myChar:FindFirstChildOfClass("Humanoid")
        if hrp then hrp.Anchored=false end
        if hum then
            hum.WalkSpeed=spectSavedWS; hum.JumpPower=spectSavedJP
            hum.AutoRotate=true; hum:ChangeState(Enum.HumanoidStateType.GettingUp)
            Camera.CameraSubject=hum
        end
    end
end
UserInputService.InputChanged:Connect(function(input)
    if _G.BZSession~=BZ_SID then return end
    if spectateTarget then
        if input.UserInputType==Enum.UserInputType.MouseMovement then
            spectOrbitY=spectOrbitY-input.Delta.X*0.005
            spectOrbitX=math.clamp(spectOrbitX-input.Delta.Y*0.005,-1.3,1.3)
        end
        if input.UserInputType==Enum.UserInputType.MouseWheel then
            spectOrbitDist=math.clamp(spectOrbitDist-input.Position.Z*2,2,50)
        end
    end
end)

-- TL System
local tlSnd=Instance.new("Sound"); tlSnd.SoundId="rbxassetid://1847323967"; tlSnd.Volume=0.6; tlSnd.Parent=workspace
local tlSGui=Instance.new("ScreenGui"); tlSGui.Name="TLGui"; tlSGui.ResetOnSpawn=false; tlSGui.IgnoreGuiInset=true
local tlNickLbl=Instance.new("TextLabel",tlSGui)
tlNickLbl.AnchorPoint=Vector2.new(1,1); tlNickLbl.Position=UDim2.new(1,-16,1,-48); tlNickLbl.Size=UDim2.new(0,200,0,24)
tlNickLbl.BackgroundTransparency=1; tlNickLbl.TextColor3=Color3.fromRGB(220,35,35); tlNickLbl.TextTransparency=1
tlNickLbl.Font=Enum.Font.GothamBold; tlNickLbl.TextSize=13; tlNickLbl.TextXAlignment=Enum.TextXAlignment.Right
tlNickLbl.TextStrokeTransparency=0.5; tlNickLbl.TextStrokeColor3=Color3.new(0,0,0)
local tlModeLbl=Instance.new("TextLabel",tlSGui)
tlModeLbl.AnchorPoint=Vector2.new(1,1); tlModeLbl.Position=UDim2.new(1,-16,1,-28); tlModeLbl.Size=UDim2.new(0,200,0,18)
tlModeLbl.BackgroundTransparency=1; tlModeLbl.TextColor3=Color3.fromRGB(120,120,120); tlModeLbl.TextTransparency=1
tlModeLbl.Font=Enum.Font.Gotham; tlModeLbl.TextSize=11; tlModeLbl.TextXAlignment=Enum.TextXAlignment.Right
tlModeLbl.Text="[ passo fantasma ativo ]"; tlModeLbl.TextStrokeTransparency=0.6; tlModeLbl.TextStrokeColor3=Color3.new(0,0,0)
task.spawn(function() tlSGui.Parent=LocalPlayer:WaitForChild("PlayerGui") end)
local function tlSetNick(n) tlNickLbl.Text=n or ""; tw(tlNickLbl,{TextTransparency=n and 0 or 1},0.15):Play() end
local function tlSetMode(on) tw(tlModeLbl,{TextTransparency=on and 0 or 1},0.2):Play() end
local function tlRemHL(p) local h=tlHLs[p]; if h then pcall(function() h:Destroy() end); tlHLs[p]=nil end end
local function tlClearAll()
    for p in pairs(tlHLs) do tlRemHL(p) end
    tlHovered=nil; tlModoAtivo=false; tlRmbHeld=false; tlSetNick(nil); tlSetMode(false)
end
RunService.RenderStepped:Connect(function()
    if _G.BZSession~=BZ_SID then return end
    if not tlModoAtivo then return end
    local cam=workspace.CurrentCamera; local sc=Vector2.new(cam.ViewportSize.X/2,cam.ViewportSize.Y/2)
    local best,bestD=nil,math.huge
    for _,p in ipairs(Players:GetPlayers()) do
        if p==LocalPlayer then continue end
        local char=p.Character; local hum=char and char:FindFirstChildOfClass("Humanoid")
        if not hum or hum.Health<=0 then continue end
        local root=char:FindFirstChild("HumanoidRootPart"); if not root then continue end
        local sp,on=cam:WorldToViewportPoint(root.Position); if not on then continue end
        local d=(Vector2.new(sp.X,sp.Y)-sc).Magnitude
        if d<=40 and d<bestD then bestD=d; best=p end
    end
    for p in pairs(tlHLs) do if p~=best then tlRemHL(p) end end
    if best then
        local h=tlHLs[best]; local char=best.Character
        if h and char and h.Adornee~=char then tlRemHL(best); h=nil end
        if not h then
            h=Instance.new("Highlight"); h.OutlineColor=Color3.fromRGB(220,35,35)
            h.FillColor=Color3.fromRGB(220,35,35); h.FillTransparency=0.72; h.OutlineTransparency=0
            h.DepthMode=Enum.HighlightDepthMode.AlwaysOnTop; h.Adornee=char; h.Parent=char; tlHLs[best]=h
        end
        tlHovered=best; tlSetNick(best.Name)
    else
        tlHovered=nil; tlSetNick(nil)
    end
end)

-- ============================================================
-- MAIN GUI
-- ============================================================
local ScreenGui=Instance.new("ScreenGui")
ScreenGui.Name="BezalelHub"; ScreenGui.ResetOnSpawn=false
ScreenGui.ZIndexBehavior=Enum.ZIndexBehavior.Sibling
ScreenGui.Parent=gethui and gethui() or game.CoreGui

local MiniBall=Instance.new("Frame",ScreenGui)
MiniBall.Size=UDim2.new(0,72,0,72); MiniBall.AnchorPoint=Vector2.new(0,0.5)
MiniBall.Position=UDim2.new(0,20,0.5,-36)
MiniBall.BackgroundColor3=Color3.fromRGB(20,0,50); MiniBall.BorderSizePixel=0
MiniBall.ZIndex=500; MiniBall.Visible=false; corner(MiniBall,99)
local ballGrad=Instance.new("UIGradient",MiniBall)
ballGrad.Color=ColorSequence.new{ColorSequenceKeypoint.new(0,Color3.fromRGB(160,30,255)),ColorSequenceKeypoint.new(1,Color3.fromRGB(60,0,140))}
ballGrad.Rotation=135

-- MiniBall drag (isolated function to reduce register count)
local function _initBallDrag()
    local bs=Instance.new("UIStroke",MiniBall); bs.Color=Color3.fromRGB(160,40,255); bs.Thickness=2.5; bs.Transparency=0.2
    if BALL_IMAGE_ID~="" then
        local ballImg=Instance.new("ImageLabel",MiniBall)
        ballImg.Size=UDim2.new(1,0,1,0); ballImg.BackgroundTransparency=1
        ballImg.Image=BALL_IMAGE_ID; ballImg.ZIndex=501; ballImg.ScaleType=Enum.ScaleType.Fit
        local ic=Instance.new("UICorner",ballImg); ic.CornerRadius=UDim.new(0,99)
    else
        local bzLbl=Instance.new("TextLabel",MiniBall)
        bzLbl.Size=UDim2.new(1,0,1,0); bzLbl.BackgroundTransparency=1
        bzLbl.Text="BZ"; bzLbl.Font=Enum.Font.GothamBold; bzLbl.TextSize=24
        bzLbl.TextColor3=Color3.new(1,1,1); bzLbl.ZIndex=501
        bzLbl.TextXAlignment=Enum.TextXAlignment.Center
        bzLbl.TextStrokeTransparency=0.4; bzLbl.TextStrokeColor3=Color3.fromRGB(130,0,255)
    end
    local ballDrag=false; local ballDragStart; local ballStartPos
    local ballClickTime=0; local ballClickN=0
    MiniBall.InputBegan:Connect(function(input)
        if input.UserInputType==Enum.UserInputType.MouseButton1 then
            local now=tick()
            if now-ballClickTime<0.38 then ballClickN=ballClickN+1 else ballClickN=1 end
            ballClickTime=now
            if ballClickN>=2 then
                ballClickN=0; MiniBall.Visible=false
                if mostrarHub then mostrarHub() end
            else
                ballDrag=true; ballDragStart=input.Position; ballStartPos=MiniBall.Position
            end
        end
    end)
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType==Enum.UserInputType.MouseButton1 then ballDrag=false end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if ballDrag and input.UserInputType==Enum.UserInputType.MouseMovement then
            local d=input.Position-ballDragStart
            MiniBall.Position=UDim2.new(ballStartPos.X.Scale,ballStartPos.X.Offset+d.X,ballStartPos.Y.Scale,ballStartPos.Y.Offset+d.Y)
        end
    end)
end
_initBallDrag()

local TooltipFrame=Instance.new("Frame",ScreenGui)
TooltipFrame.Size=UDim2.new(0,200,0,0); TooltipFrame.AutomaticSize=Enum.AutomaticSize.Y
TooltipFrame.BackgroundColor3=Color3.fromRGB(18,18,32); TooltipFrame.BorderSizePixel=0
TooltipFrame.ZIndex=300; TooltipFrame.Visible=false; corner(TooltipFrame,6); stroke(TooltipFrame,HC.Border,0.3)
local TooltipLbl=lbl(TooltipFrame,"",11,Enum.Font.Gotham,Color3.fromRGB(180,180,215),1)
TooltipLbl.Size=UDim2.new(1,-16,0,0); TooltipLbl.AutomaticSize=Enum.AutomaticSize.Y
TooltipLbl.TextWrapped=true; TooltipLbl.TextXAlignment=Enum.TextXAlignment.Left; TooltipLbl.ZIndex=301
pad(TooltipLbl,0,0,4,4); pad(TooltipFrame,8,8,6,6)

local TopNotif=Instance.new("Frame",ScreenGui)
TopNotif.AnchorPoint=Vector2.new(0.5,0); TopNotif.Size=UDim2.new(0,340,0,44)
TopNotif.Position=UDim2.new(0.5,0,0,-80)
TopNotif.BackgroundColor3=Color3.fromRGB(16,16,28); TopNotif.BackgroundTransparency=1
TopNotif.BorderSizePixel=0; TopNotif.ZIndex=200; TopNotif.Visible=false; corner(TopNotif,12)
local TopNotifStroke=stroke(TopNotif,HC.Accent,1)
local NotifStripe=Instance.new("Frame",TopNotif)
NotifStripe.Size=UDim2.new(0,4,0.7,0); NotifStripe.Position=UDim2.new(0,0,0.15,0)
NotifStripe.BackgroundColor3=HC.Success; NotifStripe.BackgroundTransparency=1; NotifStripe.BorderSizePixel=0; corner(NotifStripe,4)
local NotifIcon=lbl(TopNotif,"!",13,Enum.Font.GothamBold,HC.Accent,1)
NotifIcon.Size=UDim2.new(0,32,1,0); NotifIcon.Position=UDim2.new(0,10,0,0); NotifIcon.TextXAlignment=Enum.TextXAlignment.Center; NotifIcon.ZIndex=201
local TopNotifLbl=lbl(TopNotif,"",12,Enum.Font.GothamBold,HC.Text,1)
TopNotifLbl.Size=UDim2.new(1,-50,1,0); TopNotifLbl.Position=UDim2.new(0,48,0,0)
TopNotifLbl.TextXAlignment=Enum.TextXAlignment.Left; TopNotifLbl.ZIndex=201

local function showTopNotif(msg,cor)
    pN(); cor=cor or HC.Success
    NotifStripe.BackgroundColor3=cor; NotifIcon.TextColor3=cor; TopNotifLbl.Text=msg
    TopNotif.Visible=true
    TopNotif.Position=UDim2.new(0.5,0,0,-80); TopNotif.BackgroundTransparency=1
    TopNotifLbl.TextTransparency=1; NotifStripe.BackgroundTransparency=1
    NotifIcon.TextTransparency=1; TopNotifStroke.Transparency=1
    tw(TopNotif,{Position=UDim2.new(0.5,0,0,10),BackgroundTransparency=0.08},0.3,Enum.EasingStyle.Back):Play()
    tw(TopNotifLbl,{TextTransparency=0},0.22):Play()
    tw(NotifStripe,{BackgroundTransparency=0},0.2):Play()
    tw(NotifIcon,{TextTransparency=0},0.2):Play()
    tw(TopNotifStroke,{Transparency=0.35},0.2):Play()
    task.delay(2.8,function()
        if not TopNotif.Parent then return end
        tw(TopNotif,{Position=UDim2.new(0.5,0,0,-80),BackgroundTransparency=1},0.25,Enum.EasingStyle.Quad,Enum.EasingDirection.In):Play()
        tw(TopNotifLbl,{TextTransparency=1},0.2):Play()
        tw(NotifStripe,{BackgroundTransparency=1},0.18):Play()
        tw(NotifIcon,{TextTransparency=1},0.18):Play()
        tw(TopNotifStroke,{Transparency=1},0.18):Play()
        task.delay(0.3,function() if TopNotif.Parent then TopNotif.Visible=false end end)
    end)
end

local FpsDisplay=Instance.new("Frame",ScreenGui)
FpsDisplay.Size=UDim2.new(0,100,0,34); FpsDisplay.AnchorPoint=Vector2.new(0,0)
FpsDisplay.Position=UDim2.new(0.5,-50,0,14)
FpsDisplay.BackgroundColor3=Color3.fromRGB(12,12,20); FpsDisplay.BackgroundTransparency=0.08
FpsDisplay.BorderSizePixel=0; FpsDisplay.ZIndex=100; FpsDisplay.Visible=false; corner(FpsDisplay,9)
local fpsNormalStroke=stroke(FpsDisplay,HC.Accent,0.4)
local fpsDragStroke=stroke(FpsDisplay,Color3.fromRGB(255,210,0),1)
local FpsIconLbl=lbl(FpsDisplay,"FPS",9,Enum.Font.GothamBold,HC.TextMuted,1)
FpsIconLbl.Size=UDim2.new(0,34,1,0); FpsIconLbl.TextXAlignment=Enum.TextXAlignment.Center
local FpsValueLbl=lbl(FpsDisplay,"--",15,Enum.Font.GothamBold,HC.Accent,1)
FpsValueLbl.Size=UDim2.new(1,-36,1,0); FpsValueLbl.Position=UDim2.new(0,34,0,0)
FpsValueLbl.TextXAlignment=Enum.TextXAlignment.Left
local FpsDot=Instance.new("Frame",FpsDisplay); FpsDot.Size=UDim2.new(0,6,0,6); FpsDot.AnchorPoint=Vector2.new(1,0.5)
FpsDot.Position=UDim2.new(1,-5,0.5,0); FpsDot.BackgroundColor3=HC.Success; FpsDot.BorderSizePixel=0; corner(FpsDot,99)

local function _initFpsDrag()
    local fpsDraggable=false; local fpsDragActive=false
    local fpsDragStart; local fpsDragStartPos
    local fpsClickTime=0; local fpsClickCount=0
    FpsDisplay.InputBegan:Connect(function(input)
        if input.UserInputType==Enum.UserInputType.MouseButton1 then
            local now=tick()
            if now-fpsClickTime<0.38 then fpsClickCount=fpsClickCount+1 else fpsClickCount=1 end
            fpsClickTime=now
            if fpsClickCount>=2 then
                fpsClickCount=0; fpsDraggable=not fpsDraggable; fpsDragActive=false
                fpsDragStroke.Transparency=fpsDraggable and 0 or 1
                fpsNormalStroke.Color=fpsDraggable and Color3.fromRGB(255,210,0) or HC.Accent
            elseif fpsDraggable then
                fpsDragActive=true; fpsDragStart=input.Position; fpsDragStartPos=FpsDisplay.Position
            end
        end
    end)
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType==Enum.UserInputType.MouseButton1 then fpsDragActive=false end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if fpsDragActive and input.UserInputType==Enum.UserInputType.MouseMovement then
            local d=input.Position-fpsDragStart
            FpsDisplay.Position=UDim2.new(fpsDragStartPos.X.Scale,fpsDragStartPos.X.Offset+d.X,fpsDragStartPos.Y.Scale,fpsDragStartPos.Y.Offset+d.Y)
        end
    end)
end
_initFpsDrag()

local Main=Instance.new("Frame",ScreenGui)
Main.Name="Main"; Main.AnchorPoint=Vector2.new(0.5,0.5)
Main.Size=UDim2.new(0,hubW,0,hubH); Main.Position=UDim2.new(0.5,0,0.5,0)
Main.BackgroundColor3=HC.Background; Main.BorderSizePixel=0; Main.ClipsDescendants=true; corner(Main,18)

local Sidebar=Instance.new("Frame",Main)
Sidebar.Size=UDim2.new(0,SIDEBAR_W,1,0); Sidebar.BackgroundColor3=HC.Surface; Sidebar.BorderSizePixel=0
local SepLine=Instance.new("Frame",Main)
SepLine.Size=UDim2.new(0,1,1,0); SepLine.Position=UDim2.new(0,SIDEBAR_W,0,0)
SepLine.BackgroundColor3=HC.Border; SepLine.BackgroundTransparency=0.4; SepLine.BorderSizePixel=0

local LogoBG=Instance.new("Frame",Sidebar)
LogoBG.Size=UDim2.new(1,0,0,62); LogoBG.Position=UDim2.new(0,0,0,0)
LogoBG.BorderSizePixel=0; LogoBG.BackgroundColor3=HC.Accent; LogoBG.ZIndex=2
local LGrad=Instance.new("UIGradient",LogoBG)
LGrad.Color=ColorSequence.new{ColorSequenceKeypoint.new(0,HC.Accent),ColorSequenceKeypoint.new(1,HC.AccentDark)}
LGrad.Rotation=90

local TituloLbl=lbl(LogoBG,"Bezalel Hub",15,Enum.Font.GothamBold,Color3.new(1,1,1),1)
TituloLbl.Size=UDim2.new(1,0,0,24); TituloLbl.Position=UDim2.new(0,0,0,10)
TituloLbl.TextXAlignment=Enum.TextXAlignment.Center; TituloLbl.ZIndex=6
local VersaoLbl=lbl(LogoBG,"v1.0",10,Enum.Font.Gotham,Color3.fromRGB(200,170,255),1)
VersaoLbl.Size=UDim2.new(1,0,0,14); VersaoLbl.Position=UDim2.new(0,0,0,38)
VersaoLbl.TextXAlignment=Enum.TextXAlignment.Center; VersaoLbl.ZIndex=6

local SearchBox=Instance.new("Frame",Sidebar)
SearchBox.Size=UDim2.new(1,-14,0,27); SearchBox.Position=UDim2.new(0,7,0,68)
SearchBox.BackgroundColor3=HC.Surface2; SearchBox.BorderSizePixel=0; corner(SearchBox,7)
local searchIco=lbl(SearchBox,"🔍",11,Enum.Font.Gotham,HC.TextMuted,1)
searchIco.Size=UDim2.new(0,24,1,0); searchIco.TextXAlignment=Enum.TextXAlignment.Center
local SearchInput=Instance.new("TextBox",SearchBox)
SearchInput.Size=UDim2.new(1,-28,1,-4); SearchInput.Position=UDim2.new(0,24,0,2)
SearchInput.BackgroundTransparency=1; SearchInput.BorderSizePixel=0
SearchInput.PlaceholderText="Pesquisar..."; SearchInput.PlaceholderColor3=HC.TextMuted
SearchInput.Text=""; SearchInput.TextSize=11; SearchInput.Font=Enum.Font.Gotham
SearchInput.TextColor3=HC.Text; SearchInput.TextXAlignment=Enum.TextXAlignment.Left; SearchInput.ClearTextOnFocus=false
SearchInput.Focused:Connect(function() tw(SearchBox,{BackgroundColor3=Color3.fromRGB(38,28,58)},0.15):Play() end)
SearchInput.FocusLost:Connect(function() tw(SearchBox,{BackgroundColor3=HC.Surface2},0.15):Play() end)

local TabList=Instance.new("Frame",Sidebar)
TabList.Size=UDim2.new(1,0,1,-152); TabList.Position=UDim2.new(0,0,0,102); TabList.BackgroundTransparency=1
local TabLayout=Instance.new("UIListLayout",TabList)
TabLayout.SortOrder=Enum.SortOrder.LayoutOrder; TabLayout.Padding=UDim.new(0,2)
pad(TabList,6,6,4,4)

local StatusBar=Instance.new("Frame",Sidebar)
StatusBar.Size=UDim2.new(1,0,0,44); StatusBar.Position=UDim2.new(0,0,1,-44)
StatusBar.BackgroundColor3=HC.Surface2; StatusBar.BorderSizePixel=0
local StatusDot=Instance.new("Frame",StatusBar)
StatusDot.Size=UDim2.new(0,7,0,7); StatusDot.Position=UDim2.new(0,11,0,8)
StatusDot.BackgroundColor3=HC.Success; StatusDot.BorderSizePixel=0; corner(StatusDot,99)
local StatusLbl=lbl(StatusBar,"Conectado",10,nil,HC.TextMuted,1)
StatusLbl.Size=UDim2.new(1,-26,0,14); StatusLbl.Position=UDim2.new(0,24,0,4); StatusLbl.TextXAlignment=Enum.TextXAlignment.Left
local TickerCont=Instance.new("Frame",StatusBar)
TickerCont.Size=UDim2.new(1,-8,0,18); TickerCont.Position=UDim2.new(0,4,0,22)
TickerCont.BackgroundTransparency=1; TickerCont.ClipsDescendants=true; TickerCont.BorderSizePixel=0
local TickerLbl=lbl(TickerCont,"",10,nil,Color3.fromRGB(160,130,220),1)
TickerLbl.Size=UDim2.new(0,900,1,0); TickerLbl.TextXAlignment=Enum.TextXAlignment.Left
TickerLbl.TextTransparency=1; TickerLbl.BorderSizePixel=0

task.spawn(function()
    local ok,info=pcall(function() return MarketplaceService:GetProductInfo(game.PlaceId) end)
    local gameName=(ok and info and info.Name) or "Jogo Desconhecido"
    TickerLbl.Text=gameName.."   *   "..gameName.."   *   "..gameName.."   *   "
    task.wait(0.2)
    local pos=0
    RunService.RenderStepped:Connect(function(dt)
        if _G.BZSession~=BZ_SID then return end
        if not TickerLbl.Parent then return end
        pos=pos-28*dt
        local hw=TickerLbl.TextBounds.X/3
        if hw>0 and pos<-hw then pos=0 end
        TickerLbl.Position=UDim2.new(0,math.floor(pos),0,0)
    end)
    while TickerLbl.Parent and _G.BZSession==BZ_SID do
        tw(TickerLbl,{TextTransparency=0},0.75,Enum.EasingStyle.Sine):Play()
        task.wait(3.8)
        tw(TickerLbl,{TextTransparency=1},0.75,Enum.EasingStyle.Sine):Play()
        task.wait(1.5)
    end
end)

local ContentArea=Instance.new("Frame",Main)
ContentArea.Size=UDim2.new(1,-SIDEBAR_W,1,0); ContentArea.Position=UDim2.new(0,SIDEBAR_W,0,0)
ContentArea.BackgroundTransparency=1

local TopBar=Instance.new("Frame",ContentArea)
TopBar.Size=UDim2.new(1,0,0,48); TopBar.BackgroundColor3=HC.Surface; TopBar.BorderSizePixel=0
local TopAccent=Instance.new("Frame",TopBar)
TopAccent.Size=UDim2.new(1,0,0,2); TopAccent.Position=UDim2.new(0,0,1,-2); TopAccent.BackgroundColor3=HC.Accent; TopAccent.BorderSizePixel=0
local TAGrad=Instance.new("UIGradient",TopAccent)
TAGrad.Color=ColorSequence.new{ColorSequenceKeypoint.new(0,HC.Accent),ColorSequenceKeypoint.new(1,Color3.fromRGB(14,14,21))}
local TabTitle=lbl(TopBar,"Visual",15,Enum.Font.GothamBold,HC.Text,1)
TabTitle.Size=UDim2.new(0,160,1,-4); TabTitle.Position=UDim2.new(0,14,0,2); TabTitle.TextXAlignment=Enum.TextXAlignment.Left
local BtnClose=Instance.new("TextButton",TopBar)
BtnClose.Size=UDim2.new(0,26,0,26); BtnClose.Position=UDim2.new(1,-34,0.5,-13)
BtnClose.BackgroundColor3=HC.Danger; BtnClose.BorderSizePixel=0; BtnClose.Text="x"
BtnClose.TextColor3=Color3.new(1,1,1); BtnClose.TextSize=13; BtnClose.Font=Enum.Font.GothamBold; BtnClose.ZIndex=5; corner(BtnClose,6)
local BtnMin=Instance.new("TextButton",TopBar)
BtnMin.Size=UDim2.new(0,26,0,26); BtnMin.Position=UDim2.new(1,-66,0.5,-13)
BtnMin.BackgroundColor3=HC.Green; BtnMin.BorderSizePixel=0; BtnMin.Text="-"
BtnMin.TextColor3=Color3.new(1,1,1); BtnMin.TextSize=15; BtnMin.Font=Enum.Font.GothamBold; BtnMin.ZIndex=5; corner(BtnMin,6)
BtnClose.MouseEnter:Connect(function() tw(BtnClose,{BackgroundColor3=Color3.fromRGB(255,60,80)},0.1):Play() end)
BtnClose.MouseLeave:Connect(function() tw(BtnClose,{BackgroundColor3=HC.Danger},0.1):Play() end)
BtnMin.MouseEnter:Connect(function() tw(BtnMin,{BackgroundColor3=Color3.fromRGB(80,230,100)},0.1):Play() end)
BtnMin.MouseLeave:Connect(function() tw(BtnMin,{BackgroundColor3=HC.Green},0.1):Play() end)

local ScrollClip=Instance.new("Frame",ContentArea)
ScrollClip.Size=UDim2.new(1,0,1,-48); ScrollClip.Position=UDim2.new(0,0,0,48)
ScrollClip.BackgroundTransparency=1; ScrollClip.ClipsDescendants=true; ScrollClip.BorderSizePixel=0

local Scroll=Instance.new("ScrollingFrame",ScrollClip)
Scroll.Size=UDim2.new(1,-6,1,0); Scroll.Position=UDim2.new(0,0,0,0)
Scroll.BackgroundTransparency=1; Scroll.BorderSizePixel=0; Scroll.ScrollBarThickness=4
Scroll.ScrollBarImageColor3=HC.Accent; Scroll.CanvasSize=UDim2.new(0,0,0,0); Scroll.AutomaticCanvasSize=Enum.AutomaticSize.Y
local ScrollLayout=Instance.new("UIListLayout",Scroll)
ScrollLayout.SortOrder=Enum.SortOrder.LayoutOrder; ScrollLayout.Padding=UDim.new(0,0)
pad(Scroll,10,10,10,10)

local ResizeHandle=Instance.new("TextButton",Main)
ResizeHandle.Size=UDim2.new(0,22,0,22); ResizeHandle.AnchorPoint=Vector2.new(1,1); ResizeHandle.Position=UDim2.new(1,0,1,0)
ResizeHandle.BackgroundTransparency=1; ResizeHandle.Text="o"; ResizeHandle.TextColor3=HC.TextMuted
ResizeHandle.TextSize=13; ResizeHandle.Font=Enum.Font.GothamBold; ResizeHandle.BorderSizePixel=0; ResizeHandle.ZIndex=12

-- ============================================================
-- COMPONENT BUILDERS
-- ============================================================
local function criarHeader(pai,texto,ordem)
    local F=Instance.new("Frame",pai); F.Size=UDim2.new(1,0,0,28); F.BackgroundTransparency=1; F.LayoutOrder=ordem
    local L=lbl(F,texto,11,Enum.Font.GothamBold,HC.Accent,1)
    L.Size=UDim2.new(1,-8,1,0); L.Position=UDim2.new(0,4,0,0); L.TextXAlignment=Enum.TextXAlignment.Left
    local Line=Instance.new("Frame",F); Line.Size=UDim2.new(1,-8,0,1); Line.Position=UDim2.new(0,4,1,-2)
    Line.BackgroundColor3=HC.Accent; Line.BackgroundTransparency=0.7; Line.BorderSizePixel=0; return F
end
local function criarPill(parent,estado)
    local Pill=Instance.new("Frame",parent); Pill.Size=UDim2.new(0,42,0,22); Pill.AnchorPoint=Vector2.new(1,0.5)
    Pill.Position=UDim2.new(1,-8,0.5,0); Pill.BackgroundColor3=estado and HC.Accent or HC.Border; Pill.BorderSizePixel=0; corner(Pill,99); Pill.ZIndex=4
    local Dot=Instance.new("Frame",Pill); Dot.Size=UDim2.new(0,16,0,16)
    Dot.Position=estado and UDim2.new(1,-19,0.5,-8) or UDim2.new(0,3,0.5,-8)
    Dot.BackgroundColor3=Color3.new(1,1,1); Dot.BorderSizePixel=0; corner(Dot,99)
    local function setP(on)
        tw(Pill,{BackgroundColor3=on and HC.Accent or HC.Border},0.14):Play()
        tw(Dot,{Position=on and UDim2.new(1,-19,0.5,-8) or UDim2.new(0,3,0.5,-8)},0.14):Play()
    end
    return Pill,Dot,setP
end
local function criarKBBtn(parent,kbId,rightOff)
    local Btn=Instance.new("TextButton",parent)
    Btn.Size=UDim2.new(0,80,0,22); Btn.AnchorPoint=Vector2.new(1,0.5); Btn.Position=UDim2.new(1,rightOff or -56,0.5,0)
    Btn.BackgroundColor3=HC.Surface2; Btn.BorderSizePixel=0; Btn.Text=kbNome(KB[kbId])
    Btn.TextColor3=HC.TextMuted; Btn.TextSize=10; Btn.Font=Enum.Font.GothamBold; Btn.ZIndex=10; corner(Btn,5); stroke(Btn,HC.Border,0.4)
    local function update(kc) KB[kbId]=kc; Btn.Text=kbNome(kc); Btn.TextColor3=kc==Enum.KeyCode.Unknown and HC.TextMuted or HC.Text end
    Btn.MouseButton1Click:Connect(function()
        if kbEscutando then return end; pC(); Btn.Text="Pressione..."; Btn.TextColor3=HC.Accent
        kbEscutando=true; kbCb=function(kc) update(kc) end
    end)
    Btn.MouseButton2Click:Connect(function() update(Enum.KeyCode.Unknown) end)
    return Btn
end
local function criarInfoBtn(titleRow,texto)
    if not texto or texto=="" then return nil end
    local btn=Instance.new("TextButton",titleRow)
    btn.Size=UDim2.new(0,17,0,17); btn.BackgroundColor3=HC.Surface2; btn.BorderSizePixel=0
    btn.Text="?"; btn.TextColor3=HC.TextMuted; btn.TextSize=9; btn.Font=Enum.Font.GothamBold
    btn.ZIndex=10; corner(btn,5); btn.LayoutOrder=99
    local hT
    btn.MouseEnter:Connect(function()
        hT=task.delay(0.45,function()
            if not btn.Parent then return end; TooltipLbl.Text=texto
            local m=UserInputService:GetMouseLocation()
            TooltipFrame.Position=UDim2.new(0,m.X+14,0,m.Y-8); TooltipFrame.Visible=true
        end)
    end)
    btn.MouseLeave:Connect(function() if hT then task.cancel(hT) end; TooltipFrame.Visible=false end)
    btn.MouseMoved:Connect(function(x,y) if TooltipFrame.Visible then TooltipFrame.Position=UDim2.new(0,x+14,0,y-8) end end)
    return btn
end
local function baseCard(pai,h,ordem)
    local F=Instance.new("Frame",pai); F.Size=UDim2.new(1,0,0,h or 50)
    F.BackgroundColor3=HC.Surface2; F.BorderSizePixel=0; F.LayoutOrder=ordem or 0; corner(F,8)
    local Bar=Instance.new("Frame",F); Bar.Size=UDim2.new(0,3,0.6,0); Bar.Position=UDim2.new(0,0,0.2,0)
    Bar.BackgroundColor3=HC.Accent; Bar.BackgroundTransparency=1; Bar.BorderSizePixel=0; corner(Bar,99)
    F.MouseEnter:Connect(function() tw(F,{BackgroundColor3=Color3.fromRGB(37,37,55)},0.1):Play() end)
    F.MouseLeave:Connect(function() tw(F,{BackgroundColor3=HC.Surface2},0.1):Play() end)
    return F,Bar
end
local function makeTitleRow(parent,text,rightReserved,yOff)
    local row=Instance.new("Frame",parent); row.BackgroundTransparency=1
    row.Position=UDim2.new(0,12,0,yOff or 8); row.Size=UDim2.new(1,-(rightReserved or 60)-12,0,18)
    local rl=Instance.new("UIListLayout",row)
    rl.FillDirection=Enum.FillDirection.Horizontal; rl.VerticalAlignment=Enum.VerticalAlignment.Center
    rl.Padding=UDim.new(0,4); rl.SortOrder=Enum.SortOrder.LayoutOrder
    local nlbl=Instance.new("TextLabel",row); nlbl.BackgroundTransparency=1; nlbl.Text=text
    nlbl.Font=Enum.Font.GothamSemibold; nlbl.TextSize=13; nlbl.TextColor3=HC.Text; nlbl.LayoutOrder=1
    nlbl.AutomaticSize=Enum.AutomaticSize.X; nlbl.Size=UDim2.new(0,0,0,18); nlbl.TextXAlignment=Enum.TextXAlignment.Left; nlbl.BorderSizePixel=0
    return row,nlbl
end

local function criarRGBPicker(parent,label,defaultColor,onColorChanged,ordem)
    local curH,curS,curV=Color3.toHSV(defaultColor)
    local Card=Instance.new("Frame",parent)
    Card.Size=UDim2.new(1,-8,0,104)
    Card.BackgroundColor3=Color3.fromRGB(18,18,30); Card.BorderSizePixel=0; Card.LayoutOrder=ordem; corner(Card,7)
    local LBL=lbl(Card,label,9,Enum.Font.GothamSemibold,HC.TextMuted,1)
    LBL.Size=UDim2.new(1,-46,0,14); LBL.Position=UDim2.new(0,8,0,5); LBL.TextXAlignment=Enum.TextXAlignment.Left
    local Preview=Instance.new("Frame",Card)
    Preview.Size=UDim2.new(0,30,0,30); Preview.AnchorPoint=Vector2.new(1,0)
    Preview.Position=UDim2.new(1,-6,0,5); Preview.BackgroundColor3=defaultColor
    Preview.BorderSizePixel=0; corner(Preview,6); stroke(Preview,Color3.new(1,1,1),0.5)
    local hexLbl=lbl(Card,"",8,nil,HC.TextMuted,1)
    hexLbl.Size=UDim2.new(1,-16,0,11); hexLbl.Position=UDim2.new(0,8,0,89)
    hexLbl.TextXAlignment=Enum.TextXAlignment.Left
    local sGradRef,vGradRef
    local function makeRGBSlider(yPos,sliderLabel,initRatio,onDragFn)
        local lf=lbl(Card,sliderLabel,8,nil,HC.TextMuted,1)
        lf.Size=UDim2.new(0,14,0,10); lf.Position=UDim2.new(0,8,0,yPos+2)
        lf.TextXAlignment=Enum.TextXAlignment.Center
        local Track=Instance.new("Frame",Card)
        Track.Size=UDim2.new(1,-78,0,10); Track.Position=UDim2.new(0,26,0,yPos)
        Track.BackgroundColor3=Color3.new(1,1,1); Track.BorderSizePixel=0; corner(Track,99)
        local grad=Instance.new("UIGradient",Track)
        local Mark=Instance.new("Frame",Track)
        Mark.Size=UDim2.new(0,14,0,14); Mark.AnchorPoint=Vector2.new(0.5,0.5)
        Mark.Position=UDim2.new(initRatio,0,0.5,0); Mark.BackgroundColor3=Color3.new(1,1,1)
        Mark.BorderSizePixel=0; Mark.ZIndex=3; corner(Mark,99); stroke(Mark,Color3.fromRGB(60,60,80),0.4)
        local SBtn=Instance.new("TextButton",Track)
        SBtn.Size=UDim2.new(1,0,0,28); SBtn.Position=UDim2.new(0,0,0.5,-14)
        SBtn.BackgroundTransparency=1; SBtn.Text=""; SBtn.ZIndex=4
        SBtn.MouseButton1Down:Connect(function()
            activeDrag={track=Track,mark=Mark,isRaw=true,
                cb=function(r) Mark.Position=UDim2.new(r,0,0.5,0); onDragFn(r) end}
        end)
        return Track,grad,Mark
    end
    local function updateAll()
        local color=Color3.fromHSV(curH,curS,curV)
        Preview.BackgroundColor3=color
        local r,g,b=math.floor(color.R*255),math.floor(color.G*255),math.floor(color.B*255)
        hexLbl.Text=string.format("#%02X%02X%02X  R:%d G:%d B:%d",r,g,b,r,g,b)
        if sGradRef then
            sGradRef.Color=ColorSequence.new({ColorSequenceKeypoint.new(0,Color3.fromHSV(curH,0,math.max(curV,0.3))),ColorSequenceKeypoint.new(1,Color3.fromHSV(curH,1,math.max(curV,0.3)))})
        end
        if vGradRef then
            vGradRef.Color=ColorSequence.new({ColorSequenceKeypoint.new(0,Color3.new(0,0,0)),ColorSequenceKeypoint.new(1,Color3.fromHSV(curH,math.max(curS,0.2),1))})
        end
        if onColorChanged then onColorChanged(color) end
    end
    local _,hGrad,_=makeRGBSlider(23,"H",curH,function(r) curH=r; updateAll() end)
    local kps={}
    for i=0,6 do kps[i+1]=ColorSequenceKeypoint.new(i/6,Color3.fromHSV(i/6,1,1)) end
    hGrad.Color=ColorSequence.new(kps)
    local _,sG,_=makeRGBSlider(43,"S",curS,function(r) curS=r; updateAll() end)
    sGradRef=sG
    local _,vG,_=makeRGBSlider(63,"V",curV,function(r) curV=r; updateAll() end)
    vGradRef=vG
    updateAll()
    return Card
end

local function criarToggle(pai,nome,sub,info,padrao,cbLigar,cbDesligar,ordem)
    local Card,Bar=baseCard(pai,50,ordem); local estado=padrao or false
    local Pill,_,setPill=criarPill(Card,estado)
    local TRow,_=makeTitleRow(Card,nome,56); criarInfoBtn(TRow,info)
    local SLbl=lbl(Card,sub or "",10,nil,HC.TextMuted,1)
    SLbl.Size=UDim2.new(1,-64,0,14); SLbl.Position=UDim2.new(0,12,0,30); SLbl.TextXAlignment=Enum.TextXAlignment.Left
    Bar.BackgroundTransparency=estado and 0 or 1
    if estado and cbLigar then cbLigar() end
    local function setVisual(e) estado=e; setPill(e); Bar.BackgroundTransparency=e and 0 or 1 end
    table.insert(allToggleSetters,{fn=function() setVisual(false) end})
    local Btn=Instance.new("TextButton",Card); Btn.Size=UDim2.new(1,0,1,0); Btn.BackgroundTransparency=1; Btn.Text=""; Btn.ZIndex=1
    Btn.MouseButton1Click:Connect(function()
        pT(); estado=not estado; setPill(estado); tw(Bar,{BackgroundTransparency=estado and 0 or 1},0.15):Play()
        if estado then if cbLigar then cbLigar() end else if cbDesligar then cbDesligar() end end
    end)
    table.insert(allCards,{card=Card,tab=currentTab,name=string.lower(nome),keywords=string.lower(sub or ""),origParent=pai})
    return Card,function(e) setVisual(e); if e then if cbLigar then cbLigar() end else if cbDesligar then cbDesligar() end end end
end

local function criarToggleKB(pai,nome,sub,info,kbId,padrao,cbArmar,cbDesarmar,ordem)
    local Card,Bar=baseCard(pai,52,ordem); local estado=padrao or false
    local Pill,_,setPill=criarPill(Card,estado)
    criarKBBtn(Card,kbId,-56)
    local TRow,_=makeTitleRow(Card,nome,148); criarInfoBtn(TRow,info)
    local SLbl=lbl(Card,sub or "",10,nil,HC.TextMuted,1)
    SLbl.Size=UDim2.new(1,-156,0,14); SLbl.Position=UDim2.new(0,12,0,32); SLbl.TextXAlignment=Enum.TextXAlignment.Left
    Bar.BackgroundTransparency=estado and 0 or 1
    if estado and cbArmar then cbArmar() end
    local function setVisual(e) estado=e; setPill(e); Bar.BackgroundTransparency=e and 0 or 1 end
    table.insert(allToggleSetters,{fn=function() setVisual(false); if cbDesarmar then cbDesarmar() end end})
    local Btn=Instance.new("TextButton",Card); Btn.Size=UDim2.new(1,0,1,0); Btn.BackgroundTransparency=1; Btn.Text=""; Btn.ZIndex=1
    Btn.MouseButton1Click:Connect(function()
        pT(); estado=not estado; setPill(estado); tw(Bar,{BackgroundTransparency=estado and 0 or 1},0.15):Play()
        if estado then if cbArmar then cbArmar() end else if cbDesarmar then cbDesarmar() end end
    end)
    table.insert(allCards,{card=Card,tab=currentTab,name=string.lower(nome),keywords=string.lower(sub or ""),origParent=pai})
    return Card,function(e) setVisual(e); if e then if cbArmar then cbArmar() end else if cbDesarmar then cbDesarmar() end end end
end

local function criarSoKB(pai,nome,sub,info,kbId,ordem)
    local Card,_=baseCard(pai,50,ordem); criarKBBtn(Card,kbId,-8)
    local TRow,_=makeTitleRow(Card,nome,98); criarInfoBtn(TRow,info)
    local SLbl=lbl(Card,sub or "",10,nil,HC.TextMuted,1)
    SLbl.Size=UDim2.new(1,-106,0,14); SLbl.Position=UDim2.new(0,12,0,30); SLbl.TextXAlignment=Enum.TextXAlignment.Left
    table.insert(allCards,{card=Card,tab=currentTab,name=string.lower(nome),keywords=string.lower(sub or ""),origParent=pai})
    return Card
end

local function criarSlider(pai,nome,sub,info,minV,maxV,padrao,cb,ordem)
    local Card,_=baseCard(pai,72,ordem)
    local ratio0=(padrao-minV)/math.max(maxV-minV,1)
    local ValLbl=lbl(Card,tostring(padrao),13,Enum.Font.GothamBold,HC.Accent,1)
    ValLbl.Size=UDim2.new(0,52,0,18); ValLbl.AnchorPoint=Vector2.new(1,0); ValLbl.Position=UDim2.new(1,-8,0,7); ValLbl.TextXAlignment=Enum.TextXAlignment.Right
    local TRow,_=makeTitleRow(Card,nome,62); criarInfoBtn(TRow,info)
    local SLbl=lbl(Card,sub or "",10,nil,HC.TextMuted,1)
    SLbl.Size=UDim2.new(1,-70,0,12); SLbl.Position=UDim2.new(0,12,0,28); SLbl.TextXAlignment=Enum.TextXAlignment.Left
    local Track=Instance.new("Frame",Card); Track.Size=UDim2.new(1,-24,0,6); Track.Position=UDim2.new(0,12,0,54)
    Track.BackgroundColor3=HC.Border; Track.BorderSizePixel=0; corner(Track,99)
    local Fill=Instance.new("Frame",Track); Fill.Size=UDim2.new(ratio0,0,1,0); Fill.BackgroundColor3=HC.Accent; Fill.BorderSizePixel=0; corner(Fill,99)
    local Mark=Instance.new("Frame",Track); Mark.Size=UDim2.new(0,14,0,14); Mark.AnchorPoint=Vector2.new(0.5,0.5)
    Mark.Position=UDim2.new(ratio0,0,0.5,0); Mark.BackgroundColor3=Color3.new(1,1,1); Mark.BorderSizePixel=0; Mark.ZIndex=2; corner(Mark,99)
    local SBtn=Instance.new("TextButton",Track); SBtn.Size=UDim2.new(1,0,0,26); SBtn.Position=UDim2.new(0,0,0.5,-13)
    SBtn.BackgroundTransparency=1; SBtn.Text=""; SBtn.ZIndex=3
    SBtn.MouseButton1Down:Connect(function() pC(); activeDrag={track=Track,fill=Fill,mark=Mark,valLbl=ValLbl,minV=minV,maxV=maxV,cb=cb} end)
    table.insert(allCards,{card=Card,tab=currentTab,name=string.lower(nome),keywords=string.lower(sub or ""),origParent=pai})
    return Card
end

local function criarBotao(pai,nome,sub,info,acao,ordem)
    local Card,_=baseCard(pai,50,ordem)
    local TRow,_=makeTitleRow(Card,nome,88); criarInfoBtn(TRow,info)
    local SLbl=lbl(Card,sub or "",10,nil,HC.TextMuted,1)
    SLbl.Size=UDim2.new(1,-96,0,14); SLbl.Position=UDim2.new(0,12,0,30); SLbl.TextXAlignment=Enum.TextXAlignment.Left
    local BtnEx=Instance.new("TextButton",Card); BtnEx.Size=UDim2.new(0,76,0,28)
    BtnEx.AnchorPoint=Vector2.new(1,0.5); BtnEx.Position=UDim2.new(1,-8,0.5,0)
    BtnEx.BackgroundColor3=HC.Accent; BtnEx.BorderSizePixel=0; BtnEx.Text="Executar"
    BtnEx.TextColor3=Color3.new(1,1,1); BtnEx.TextSize=11; BtnEx.Font=Enum.Font.GothamBold; BtnEx.ZIndex=5; corner(BtnEx,6)
    BtnEx.MouseButton1Click:Connect(function()
        pC(); tw(BtnEx,{BackgroundColor3=HC.AccentDark},0.1):Play()
        task.delay(0.15,function() tw(BtnEx,{BackgroundColor3=HC.Accent},0.1):Play() end)
        if acao then acao() end
    end)
    table.insert(allCards,{card=Card,tab=currentTab,name=string.lower(nome),keywords=string.lower(sub or ""),origParent=pai})
    return Card,BtnEx
end

local function criarSubToggle(pai,nome,info,padrao,cb,ordem)
    local Card=Instance.new("Frame",pai); Card.Size=UDim2.new(1,-8,0,38)
    Card.BackgroundColor3=Color3.fromRGB(26,26,40); Card.BorderSizePixel=0; Card.LayoutOrder=ordem; corner(Card,6)
    local estado=padrao or false
    local Pill,_,setPill=criarPill(Card,estado); Pill.Size=UDim2.new(0,36,0,18); Pill.Position=UDim2.new(1,-8,0.5,0)
    local TRow,NLbl=makeTitleRow(Card,nome,50,0)
    TRow.Position=UDim2.new(0,10,0.5,-9); TRow.Size=UDim2.new(1,-60,0,18)
    NLbl.TextSize=11; NLbl.Font=Enum.Font.GothamSemibold; NLbl.TextColor3=estado and HC.Text or HC.TextMuted
    if info and info~="" then criarInfoBtn(TRow,info) end
    if estado and cb then cb(true) end
    local Btn=Instance.new("TextButton",Card); Btn.Size=UDim2.new(1,0,1,0); Btn.BackgroundTransparency=1; Btn.Text=""; Btn.ZIndex=1
    Btn.MouseButton1Click:Connect(function()
        pC(); estado=not estado; setPill(estado); NLbl.TextColor3=estado and HC.Text or HC.TextMuted
        if cb then cb(estado) end
    end)
    return Card,function(e) estado=e; setPill(e); NLbl.TextColor3=e and HC.Text or HC.TextMuted; if cb then cb(e) end end
end

local function criarItemBloqueado(pai,nome,ordem)
    local F=Instance.new("Frame",pai); F.Size=UDim2.new(1,0,0,42)
    F.BackgroundColor3=Color3.fromRGB(20,20,32); F.BorderSizePixel=0; F.LayoutOrder=ordem; corner(F,7)
    stroke(F,HC.Border,0.6)
    local nl=lbl(F,nome,12,Enum.Font.GothamSemibold,HC.TextMuted,1)
    nl.Size=UDim2.new(1,-100,1,0); nl.Position=UDim2.new(0,12,0,0); nl.TextXAlignment=Enum.TextXAlignment.Left
    local badge=Instance.new("Frame",F); badge.Size=UDim2.new(0,88,0,24); badge.AnchorPoint=Vector2.new(1,0.5)
    badge.Position=UDim2.new(1,-8,0.5,0); badge.BackgroundColor3=Color3.fromRGB(25,25,40); badge.BorderSizePixel=0; corner(badge,5)
    stroke(badge,HC.Border,0.4)
    local bl=lbl(badge,"Em breve",9,Enum.Font.GothamBold,HC.TextMuted,1)
    bl.Size=UDim2.new(1,0,1,0); bl.TextXAlignment=Enum.TextXAlignment.Center
    return F
end

-- ============================================================
-- TAB SYSTEM
-- ============================================================
local Pages={}; local TabBtns={}; local ActiveTab=nil
local TabNames={"Visual","PVP","Skills","AutoFarm","Jogadores","Amigos","TP","Misc","Creditos"}
local TabIcons={"👁","🎯","⚡","🌾","👥","👋","📍","⚙","ℹ"}

local SearchResultsPage=Instance.new("Frame",Scroll)
SearchResultsPage.Name="SearchPage"; SearchResultsPage.Size=UDim2.new(1,0,0,0)
SearchResultsPage.BackgroundTransparency=1; SearchResultsPage.Visible=false; SearchResultsPage.LayoutOrder=0; SearchResultsPage.AutomaticSize=Enum.AutomaticSize.Y
local SRL=Instance.new("UIListLayout",SearchResultsPage); SRL.Padding=UDim.new(0,6); SRL.SortOrder=Enum.SortOrder.LayoutOrder

trocarAba=function(nome)
    if ActiveTab==nome then return end
    pC(); ActiveTab=nome; TabTitle.Text=nome; SearchInput.Text=""
    SearchResultsPage.Visible=false
    for n,p in pairs(Pages) do p.Visible=(n==nome) end
    for n,b in pairs(TabBtns) do
        local on=(n==nome)
        tw(b,{BackgroundColor3=on and HC.Accent or Color3.new(0,0,0),BackgroundTransparency=on and 0 or 1},0.15):Play()
        b.TextColor3=on and Color3.new(1,1,1) or HC.TextMuted
    end
    Scroll.CanvasPosition=Vector2.new(0,0)
end

for i,nome in ipairs(TabNames) do
    local BtnTab=Instance.new("TextButton",TabList)
    BtnTab.Size=UDim2.new(1,0,0,30); BtnTab.BackgroundColor3=Color3.new(0,0,0)
    BtnTab.BackgroundTransparency=1; BtnTab.BorderSizePixel=0
    BtnTab.Text=TabIcons[i].." "..nome; BtnTab.TextSize=11; BtnTab.Font=Enum.Font.GothamSemibold
    BtnTab.TextColor3=HC.TextMuted; BtnTab.TextXAlignment=Enum.TextXAlignment.Left
    BtnTab.LayoutOrder=i; corner(BtnTab,7); pad(BtnTab,10,8,0,0)
    TabBtns[nome]=BtnTab
    BtnTab.MouseEnter:Connect(function() if ActiveTab~=nome then tw(BtnTab,{BackgroundTransparency=0.82,BackgroundColor3=HC.Accent},0.1):Play() end end)
    BtnTab.MouseLeave:Connect(function() if ActiveTab~=nome then tw(BtnTab,{BackgroundTransparency=1},0.1):Play() end end)
    BtnTab.MouseButton1Click:Connect(function() trocarAba(nome) end)
    local Pag=Instance.new("Frame",Scroll)
    Pag.Name=nome.."Page"; Pag.Size=UDim2.new(1,0,0,0); Pag.BackgroundTransparency=1
    Pag.Visible=false; Pag.AutomaticSize=Enum.AutomaticSize.Y; Pag.LayoutOrder=i+1
    local PL=Instance.new("UIListLayout",Pag); PL.SortOrder=Enum.SortOrder.LayoutOrder; PL.Padding=UDim.new(0,6)
    Pages[nome]=Pag
end

-- ============================================================
-- ABA VISUAL — separada em funcao para nao estourar os 200 registros
-- ============================================================
local function _buildVisualTab()
    currentTab="Visual"; local vis=Pages["Visual"]
    criarHeader(vis,"[ ESP ]",1)
    local espMainCard,espBar=baseCard(vis,50,2); local espEstado=true
    local espPill2,_,espSetPill2=criarPill(espMainCard,true)
    local espTRow,_=makeTitleRow(espMainCard,"ESP",56)
    criarInfoBtn(espTRow,"Exibe informacoes visuais acima dos jogadores.")
    local espSLbl=lbl(espMainCard,"Informacoes visuais sobre jogadores",10,nil,HC.TextMuted,1)
    espSLbl.Size=UDim2.new(1,-64,0,14); espSLbl.Position=UDim2.new(0,12,0,30); espSLbl.TextXAlignment=Enum.TextXAlignment.Left
    espBar.BackgroundTransparency=0
    local espSubCont=Instance.new("Frame",vis); espSubCont.Size=UDim2.new(1,0,0,0)
    espSubCont.BackgroundTransparency=1; espSubCont.AutomaticSize=Enum.AutomaticSize.Y; espSubCont.LayoutOrder=3
    local espSubLL=Instance.new("UIListLayout",espSubCont); espSubLL.Padding=UDim.new(0,4); espSubLL.SortOrder=Enum.SortOrder.LayoutOrder
    pad(espSubCont,8,0,0,0)
    espSubCont.Visible=true
    local espMainBtn=Instance.new("TextButton",espMainCard); espMainBtn.Size=UDim2.new(1,0,0,50); espMainBtn.BackgroundTransparency=1; espMainBtn.Text=""; espMainBtn.ZIndex=1
    espMainBtn.MouseButton1Click:Connect(function()
        pT(); espEstado=not espEstado; espSetPill2(espEstado); espAtivo=espEstado
        tw(espBar,{BackgroundTransparency=espEstado and 0 or 1},0.15):Play()
        espSubCont.Visible=espEstado
        showTopNotif("ESP "..(espEstado and "ativado" or "desativado"),espEstado and HC.Accent or HC.Border)
    end)
    table.insert(allToggleSetters,{fn=function()
        espEstado=false; espSetPill2(false); espAtivo=false; espBar.BackgroundTransparency=1
        espSubCont.Visible=false
    end})
    table.insert(allCards,{card=espMainCard,tab="Visual",name="esp",keywords="radar visual jogadores",origParent=vis})
    criarSubToggle(espSubCont,"Nomes","",true,function(on) espNome=on end,2)
    criarSubToggle(espSubCont,"HP","",true,function(on) espHP=on end,3)
    local hlColorCard=criarRGBPicker(espSubCont,"Cor do Highlight",espHLColor,function(c) espHLColor=c; updateHLColor() end,5)
    hlColorCard.Visible=false
    local hlIntCard=criarSlider(espSubCont,"Intensidade Highlight","Transparencia do fill (0=opaco)","0=solido | 10=invisivel",0,10,8,function(v)
        espHLFillT=v/10; updateHLColor()
    end,6)
    hlIntCard.Visible=false
    criarSubToggle(espSubCont,"Highlight","",false,function(on)
        espHighlight=on; updateEspHL()
        hlColorCard.Visible=on; hlIntCard.Visible=on
    end,4)
    local distColorCard=criarRGBPicker(espSubCont,"Cor da Distancia",espDistColor,function(c) espDistColor=c end,8)
    distColorCard.Visible=false
    criarSubToggle(espSubCont,"Distancia","",false,function(on)
        espDist=on; distColorCard.Visible=on
    end,7)

    criarHeader(vis,"[ CAMERA LIVRE ]",20)
    criarToggleKB(vis,"Camera Livre","WASD | E/Q | Shift=rapido | Ctrl=lento | Scroll=zoom",
        "Congela o personagem e libera a camera. Mouse nao afeta o personagem. Ctrl=lento, Shift=rapido, Scroll=zoom.","freecam",false,
        function() kbGated.freecam=true end,
        function() kbGated.freecam=false; if freecamAtivo then toggleFreecam() end end,21)

    criarHeader(vis,"[ VISAO ]",25)
    criarSoKB(vis,"Visao Sensorial","Destaca inimigos em vermelho por 5s",
        "Escolha uma tecla. Pressione para ativar.","vision",26)
end
_buildVisualTab()

-- ============================================================
-- ABA PVP
-- ============================================================
local function _buildPVPTab()
    currentTab="PVP"; local pvp=Pages["PVP"]
    criarHeader(pvp,"[ AIMBOT ]",1)
    criarToggle(pvp,"Aim Assist","Segure RMB: gruda na mira se tiver alguem | Teleporte do alvo libera",
        "Segure RMB com alguem na mira para travar. Se nao tiver ninguem na FOV, nao grava. Se o alvo teleportar, libera automaticamente.",false,
        function() aimAtivo=true; showTopNotif("Aim Assist ON — segure RMB com alvo na mira",HC.Accent) end,
        function() aimAtivo=false; aimLockedTarget=nil; aimLastPos=nil; showTopNotif("Aim Assist desativado",HC.Border) end,2)
    criarSlider(pvp,"FOV do Aimbot","Raio de deteccao em graus","Quanto menor, mais preciso.",1,20,8,function(v) aimFOV=v end,3)
    criarSlider(pvp,"Suavidade","0=instantaneo | 10=suave","Controla a gradualidade do movimento.",0,10,0,function(v) aimSmooth=v end,4)

    criarHeader(pvp,"[ PASSO FANTASMA ]",10)
    criarSoKB(pvp,"Passo Fantasma","Ative, use 1x (RMB hover + LMB), desativa sozinho | Max: "..maxTlDist.."st",
        "Ative com a tecla. Segure RMB sobre o alvo e clique LMB para teletransportar. Tem limite de distancia. Desativa automaticamente.","tl",11)
    criarSlider(pvp,"Limite de Distancia","Distancia maxima do Passo Fantasma","Se o alvo estiver mais longe, o TP nao ocorre.",10,300,maxTlDist,function(v) maxTlDist=v end,12)
end
_buildPVPTab()

-- ============================================================
-- ABA SKILLS
-- ============================================================
local function _buildSkillsTab()
    currentTab="Skills"; local hab=Pages["Skills"]
    criarHeader(hab,"[ VOO ]",1)
    criarToggleKB(hab,"Voar","WASD para mover | camera controla direcao",
        "Toggle arma. Escolha uma tecla para ativar/desativar.","fly",false,
        function() kbGated.fly=true end,
        function() kbGated.fly=false; if flyAtivo then flyAtivo=false; stopFly() end end,2)
    criarSlider(hab,"Velocidade do Voo","Velocidade base ao voar","Velocidade normal sem Shift.",50,500,150,function(v) flySpeed=v end,3)
    criarSlider(hab,"Boost (Shift)","Velocidade ao segurar Shift","Mantenha Shift para ativar o boost.",100,1000,400,function(v) flyBoost=v end,4)

    criarHeader(hab,"[ FISICA ]",5)
    do
        local spCard,spBar=baseCard(hab,78,6); local spEstado=false
        local spPill2,_,spSetPill2=criarPill(spCard,false)
        criarKBBtn(spCard,"jump",-56)
        local spTRow,_=makeTitleRow(spCard,"Super Pulo",148)
        criarInfoBtn(spTRow,"Toggle arma. Escolha uma tecla para ativar/desativar.")
        local spSub=lbl(spCard,"Impulso vertical potenciado no pulo",10,nil,HC.TextMuted,1)
        spSub.Size=UDim2.new(1,-156,0,14); spSub.Position=UDim2.new(0,12,0,32); spSub.TextXAlignment=Enum.TextXAlignment.Left
        local spHLbl=lbl(spCard,"Altura:  120",10,nil,HC.Accent,1)
        spHLbl.Size=UDim2.new(0,88,0,14); spHLbl.Position=UDim2.new(0,12,0,53); spHLbl.TextXAlignment=Enum.TextXAlignment.Left
        local hTrack=Instance.new("Frame",spCard); hTrack.Size=UDim2.new(1,-110,0,6); hTrack.Position=UDim2.new(0,104,0,56)
        hTrack.BackgroundColor3=HC.Border; hTrack.BorderSizePixel=0; corner(hTrack,99)
        local hFill=Instance.new("Frame",hTrack); hFill.Size=UDim2.new((120-50)/(500-50),0,1,0); hFill.BackgroundColor3=HC.Accent; hFill.BorderSizePixel=0; corner(hFill,99)
        local hMark=Instance.new("Frame",hTrack); hMark.Size=UDim2.new(0,14,0,14); hMark.AnchorPoint=Vector2.new(0.5,0.5)
        hMark.Position=UDim2.new((120-50)/(500-50),0,0.5,0); hMark.BackgroundColor3=Color3.new(1,1,1); hMark.BorderSizePixel=0; hMark.ZIndex=2; corner(hMark,99)
        local hBtn=Instance.new("TextButton",hTrack); hBtn.Size=UDim2.new(1,0,0,24); hBtn.Position=UDim2.new(0,0,0.5,-12)
        hBtn.BackgroundTransparency=1; hBtn.Text=""; hBtn.ZIndex=3
        hBtn.MouseButton1Down:Connect(function()
            pC(); activeDrag={track=hTrack,fill=hFill,mark=hMark,valLbl=spHLbl,minV=50,maxV=500,
                cb=function(v) jumpHeight=v; spHLbl.Text="Altura:  "..v end}
        end)
        local spTogBtn=Instance.new("TextButton",spCard); spTogBtn.Size=UDim2.new(1,0,0,48); spTogBtn.BackgroundTransparency=1; spTogBtn.Text=""; spTogBtn.ZIndex=1
        spTogBtn.MouseButton1Click:Connect(function()
            pT(); spEstado=not spEstado; spSetPill2(spEstado); kbGated.jump=spEstado
            if not spEstado then superPuloAtivo=false end
            tw(spBar,{BackgroundTransparency=spEstado and 0 or 1},0.15):Play()
            showTopNotif("Super Pulo "..(spEstado and "armado" or "desativado"),spEstado and HC.Accent or HC.Border)
        end)
        table.insert(allCards,{card=spCard,tab="Skills",name="super pulo",keywords="pulo altura salto jump",origParent=hab})
        table.insert(allToggleSetters,{fn=function() spEstado=false; spSetPill2(false); kbGated.jump=false; superPuloAtivo=false; spBar.BackgroundTransparency=1 end})
    end

    criarHeader(hab,"[ STEALTH ]",10)
    criarToggleKB(hab,"Invisivel","Usa cadeira invisivel para ocultar o personagem",
        "Toggle arma. Escolha uma tecla para ativar/desativar.","invis",false,
        function() kbGated.invis=true end,
        function() kbGated.invis=false; if invisAtivo then invisAtivo=true; toggleInvis() end end,11)
    criarToggleKB(hab,"Noclip","Atravessa paredes e objetos solidos",
        "Toggle arma. Escolha uma tecla para ativar/desativar.","noclip",false,
        function() kbGated.noclip=true end,
        function() kbGated.noclip=false; if noclipAtivo then toggleNoclip() end end,12)

    criarHeader(hab,"[ PROXIMITY FLING ]",20)
    criarToggleKB(hab,"Proximity Fling","Arremessa jogadores no raio de deteccao",
        "Arremessa automaticamente todos os jogadores dentro do raio configurado.",
        "fling",false,
        function() kbGated.fling=true end,
        function()
            kbGated.fling=false
            if flingAtivo then
                flingAtivo=false
                workspace.FallenPartsDestroyHeight=flingFPDH
            end
        end,21)
    criarSlider(hab,"Raio do Fling","Distancia maxima para detectar alvos","Jogadores fora desse raio serao ignorados.",5,100,20,
        function(v) flingRadius=v end,22)
end
_buildSkillsTab()

-- ============================================================
-- ABA AUTOFARM
-- ============================================================
local function _buildAutoFarmTab()
    currentTab="AutoFarm"; local af=Pages["AutoFarm"]
    local warnCard=Instance.new("Frame",af); warnCard.Size=UDim2.new(1,0,0,54); warnCard.LayoutOrder=0
    warnCard.BackgroundColor3=Color3.fromRGB(35,22,12); warnCard.BorderSizePixel=0; corner(warnCard,8)
    stroke(warnCard,Color3.fromRGB(200,120,30),0.5)
    local wlbl=lbl(warnCard,"Em breve serei lancado o AutoFarm completo!\nNenhuma funcao abaixo esta ativa ainda.",11,Enum.Font.GothamSemibold,Color3.fromRGB(220,150,60),1)
    wlbl.Size=UDim2.new(1,-16,1,0); wlbl.Position=UDim2.new(0,8,0,0)
    wlbl.TextWrapped=true; wlbl.TextXAlignment=Enum.TextXAlignment.Left; wlbl.TextYAlignment=Enum.TextYAlignment.Center
    criarHeader(af,"[ MISSAO BOSS ]",1); criarItemBloqueado(af,"Auto Missao Boss",2)
    criarHeader(af,"[ MISSAO NPC ]",3); criarItemBloqueado(af,"Auto Missao NPC",4)
    criarHeader(af,"[ MISSAO BIJUU ]",5); criarItemBloqueado(af,"Auto Missao Bijuu",6)
    criarHeader(af,"[ STATUS ]",7)
    criarItemBloqueado(af,"Chi",8); criarItemBloqueado(af,"Ninjutsu",9)
    criarItemBloqueado(af,"Taijutsu",10); criarItemBloqueado(af,"HP",11)
    criarHeader(af,"[ AUTO RANK UP ]",12); criarItemBloqueado(af,"Auto Rank Up",13)
end
_buildAutoFarmTab()

-- ============================================================
-- ABA JOGADORES
-- ============================================================
local function _buildJogadoresTab()
    currentTab="Jogadores"; local jogPag=Pages["Jogadores"]
    criarHeader(jogPag,"[ JOGADORES ]",1)
    local _,rBtn=criarBotao(jogPag,"Atualizar Lista","Atualiza a lista de jogadores online",nil,nil,2)
    rBtn.Text="Atualizar"
    local plFrame=Instance.new("Frame",jogPag); plFrame.Size=UDim2.new(1,0,0,0)
    plFrame.BackgroundTransparency=1; plFrame.AutomaticSize=Enum.AutomaticSize.Y; plFrame.LayoutOrder=3
    local pLL=Instance.new("UIListLayout",plFrame); pLL.Padding=UDim.new(0,6); pLL.SortOrder=Enum.SortOrder.LayoutOrder

    local function refreshPlayerList()
        for _,c in pairs(plFrame:GetChildren()) do if not c:IsA("UIListLayout") then c:Destroy() end end
        local idx=0
        for _,p in ipairs(Players:GetPlayers()) do
            if p==LocalPlayer then continue end; idx=idx+1
            local card=Instance.new("Frame",plFrame); card.Size=UDim2.new(1,0,0,62)
            card.BackgroundColor3=HC.Surface2; card.BorderSizePixel=0; card.LayoutOrder=idx; corner(card,8)
            local avatarFrame=Instance.new("Frame",card); avatarFrame.Size=UDim2.new(0,44,0,44)
            avatarFrame.Position=UDim2.new(0,8,0.5,-22); avatarFrame.BackgroundColor3=HC.Surface; avatarFrame.BorderSizePixel=0; corner(avatarFrame,6)
            local avatarImg=Instance.new("ImageLabel",avatarFrame); avatarImg.Size=UDim2.new(1,0,1,0)
            avatarImg.BackgroundTransparency=1; avatarImg.BorderSizePixel=0; corner(avatarImg,6)
            task.spawn(function()
                local ok,img=pcall(function() return Players:GetUserThumbnailAsync(p.UserId,Enum.ThumbnailType.HeadShot,Enum.ThumbnailSize.Size48x48) end)
                if ok and avatarImg.Parent then avatarImg.Image=img end
            end)
            local robloxNameLbl=lbl(card,p.Name,12,Enum.Font.GothamBold,HC.Text,1)
            robloxNameLbl.Size=UDim2.new(1,-214,0,17); robloxNameLbl.Position=UDim2.new(0,60,0,10)
            robloxNameLbl.TextXAlignment=Enum.TextXAlignment.Left
            local gameNameLbl=lbl(card,p.DisplayName,10,Enum.Font.Gotham,HC.TextMuted,1)
            gameNameLbl.Size=UDim2.new(1,-214,0,14); gameNameLbl.Position=UDim2.new(0,60,0,28)
            gameNameLbl.TextXAlignment=Enum.TextXAlignment.Left
            -- Botao Ver (spectate)
            local specBtn=Instance.new("TextButton",card); specBtn.Size=UDim2.new(0,42,0,24)
            specBtn.AnchorPoint=Vector2.new(1,0.5); specBtn.Position=UDim2.new(1,-8,0.5,0)
            specBtn.BackgroundColor3=HC.Info; specBtn.BorderSizePixel=0; specBtn.Text="Ver"
            specBtn.TextColor3=Color3.new(1,1,1); specBtn.TextSize=10; specBtn.Font=Enum.Font.GothamBold; specBtn.ZIndex=5; corner(specBtn,6)
            specBtn.MouseButton1Click:Connect(function()
                pC()
                if spectateTarget==p then
                    stopSpectate(); specBtn.Text="Ver"; specBtn.BackgroundColor3=HC.Info
                    showTopNotif("Parou de ver "..p.Name,HC.Border)
                else
                    if spectateTarget then
                        for _,c2 in pairs(plFrame:GetChildren()) do
                            if c2:IsA("Frame") then
                                local sb=c2:FindFirstChildOfClass("TextButton")
                                if sb and sb.Text=="Parar" then sb.Text="Ver"; sb.BackgroundColor3=HC.Info end
                            end
                        end
                    end
                    startSpectate(p); specBtn.Text="Parar"; specBtn.BackgroundColor3=HC.Danger
                    showTopNotif("Vendo: "..p.Name.." | Mouse=orbitar | Scroll=zoom",HC.Info)
                end
            end)
            -- Botao TP
            local tpBtn2=Instance.new("TextButton",card); tpBtn2.Size=UDim2.new(0,42,0,24)
            tpBtn2.AnchorPoint=Vector2.new(1,0.5); tpBtn2.Position=UDim2.new(1,-56,0.5,0)
            tpBtn2.BackgroundColor3=HC.Accent; tpBtn2.BorderSizePixel=0; tpBtn2.Text="TP"
            tpBtn2.TextColor3=Color3.new(1,1,1); tpBtn2.TextSize=11; tpBtn2.Font=Enum.Font.GothamBold; tpBtn2.ZIndex=5; corner(tpBtn2,6)
            tpBtn2.MouseButton1Click:Connect(function()
                pC(); local ch=p.Character; if not ch then return end
                local rt=ch:FindFirstChild("HumanoidRootPart"); if not rt then return end
                local ok2,myRoot=pcall(getHRP); if not ok2 then return end
                myRoot.CFrame=rt.CFrame*CFrame.new(0,0,3); showTopNotif("TP -> "..p.Name,HC.Accent)
            end)
            -- Botao Fling
            local flingBtn=Instance.new("TextButton",card); flingBtn.Size=UDim2.new(0,42,0,24)
            flingBtn.AnchorPoint=Vector2.new(1,0.5); flingBtn.Position=UDim2.new(1,-104,0.5,0)
            flingBtn.BackgroundColor3=HC.Danger; flingBtn.BorderSizePixel=0; flingBtn.Text="Fling"
            flingBtn.TextColor3=Color3.new(1,1,1); flingBtn.TextSize=10; flingBtn.Font=Enum.Font.GothamBold; flingBtn.ZIndex=5; corner(flingBtn,6)
            flingBtn.MouseButton1Click:Connect(function()
                pC()
                local ch=p.Character; if not ch then showTopNotif("Personagem de "..p.Name.." nao encontrado",HC.Danger); return end
                local rt=ch:FindFirstChild("HumanoidRootPart"); if not rt then return end
                local ok2,myRoot=pcall(getHRP); if not ok2 then return end
                showTopNotif("Fling -> "..p.Name,HC.Danger)
                task.spawn(function() pcall(doFling,p) end)
            end)
            -- Clique no card = copiar nick
            local copyArea=Instance.new("TextButton",card); copyArea.Size=UDim2.new(1,0,1,0)
            copyArea.BackgroundTransparency=1; copyArea.Text=""; copyArea.ZIndex=2
            copyArea.MouseButton1Click:Connect(function()
                pC(); pcall(function() setclipboard(p.Name) end)
                showTopNotif("Nick de "..p.Name.." copiado!",HC.Success)
            end)
            card.MouseEnter:Connect(function() tw(card,{BackgroundColor3=Color3.fromRGB(37,37,55)},0.1):Play() end)
            card.MouseLeave:Connect(function() tw(card,{BackgroundColor3=HC.Surface2},0.1):Play() end)
        end
        if idx==0 then
            local nc=Instance.new("Frame",plFrame); nc.Size=UDim2.new(1,0,0,44); nc.BackgroundColor3=HC.Surface2; nc.BorderSizePixel=0; nc.LayoutOrder=1; corner(nc,8)
            local nl=lbl(nc,"Nenhum jogador encontrado",11,nil,HC.TextMuted,1); nl.Size=UDim2.new(1,0,1,0); nl.TextXAlignment=Enum.TextXAlignment.Center
        end
    end
    rBtn.MouseButton1Click:Connect(function() pC(); refreshPlayerList(); showTopNotif("Lista atualizada",HC.Success) end)
    task.spawn(refreshPlayerList)
    Players.PlayerAdded:Connect(function() task.wait(1); refreshPlayerList() end)
    Players.PlayerRemoving:Connect(function() task.wait(0.5); refreshPlayerList() end)
end
_buildJogadoresTab()

-- ============================================================
-- ABA AMIGOS
-- ============================================================
local function _buildAmigosTab()
    currentTab="Amigos"; local amigosPag=Pages["Amigos"]
    criarHeader(amigosPag,"[ AMIGOS ONLINE ]",1)

    local _,aRefBtn=criarBotao(amigosPag,"Atualizar Amigos","Busca amigos online via API do Roblox","Requer executor com acesso HTTP.",nil,2)
    aRefBtn.Text="Atualizar"

    local loadingCard=Instance.new("Frame",amigosPag); loadingCard.Size=UDim2.new(1,0,0,38)
    loadingCard.BackgroundColor3=HC.Surface2; loadingCard.BorderSizePixel=0; loadingCard.LayoutOrder=3; corner(loadingCard,8)
    loadingCard.Visible=false
    local loadLbl=lbl(loadingCard,"🔄  Buscando amigos online...",11,nil,HC.TextMuted,1)
    loadLbl.Size=UDim2.new(1,0,1,0); loadLbl.TextXAlignment=Enum.TextXAlignment.Center

    local aFrame=Instance.new("Frame",amigosPag); aFrame.Size=UDim2.new(1,0,0,0)
    aFrame.BackgroundTransparency=1; aFrame.AutomaticSize=Enum.AutomaticSize.Y; aFrame.LayoutOrder=4
    local aLL=Instance.new("UIListLayout",aFrame); aLL.Padding=UDim.new(0,6); aLL.SortOrder=Enum.SortOrder.LayoutOrder

    local function getGameThumb(universeId,imgLabel)
        task.spawn(function()
            local ok,result=pcall(function()
                return game:HttpGet("https://thumbnails.roblox.com/v1/games/icons?universeIds="..tostring(universeId).."&size=150x150&format=Png&isCircular=false")
            end)
            if not ok or not result then return end
            local ok2,data=pcall(function() return HttpService:JSONDecode(result) end)
            if ok2 and data and data.data and data.data[1] and imgLabel.Parent then
                imgLabel.Image=data.data[1].imageUrl or ""
            end
        end)
    end

    local function refreshAmigos()
        for _,c in pairs(aFrame:GetChildren()) do if not c:IsA("UIListLayout") then c:Destroy() end end
        loadingCard.Visible=true

        task.spawn(function()
            local ok,result=pcall(function()
                return game:HttpGet("https://friends.roblox.com/v1/users/"..LocalPlayer.UserId.."/friends/online?userSort=StatusFrequency")
            end)
            loadingCard.Visible=false
            if not ok or not result then
                local errCard=Instance.new("Frame",aFrame); errCard.Size=UDim2.new(1,0,0,54)
                errCard.BackgroundColor3=Color3.fromRGB(35,14,14); errCard.BorderSizePixel=0; errCard.LayoutOrder=1; corner(errCard,8)
                stroke(errCard,HC.Danger,0.5)
                local el=lbl(errCard,"❌  Erro ao buscar amigos.\nVerifique se o executor permite HttpGet.",10,nil,HC.Danger,1)
                el.Size=UDim2.new(1,-16,1,0); el.Position=UDim2.new(0,8,0,0)
                el.TextWrapped=true; el.TextXAlignment=Enum.TextXAlignment.Left; el.TextYAlignment=Enum.TextYAlignment.Center
                return
            end
            local ok2,data=pcall(function() return HttpService:JSONDecode(result) end)
            if not ok2 or not data then
                local errCard=Instance.new("Frame",aFrame); errCard.Size=UDim2.new(1,0,0,38)
                errCard.BackgroundColor3=HC.Surface2; errCard.BorderSizePixel=0; errCard.LayoutOrder=1; corner(errCard,8)
                local el=lbl(errCard,"Erro ao processar resposta da API.",11,nil,HC.Danger,1)
                el.Size=UDim2.new(1,0,1,0); el.TextXAlignment=Enum.TextXAlignment.Center
                return
            end
            local friends=data.data or {}
            local onlineFriends={}
            for _,f in ipairs(friends) do
                if f.userPresenceType and f.userPresenceType>=1 then
                    table.insert(onlineFriends,f)
                end
            end
            if #onlineFriends==0 then
                local nc=Instance.new("Frame",aFrame); nc.Size=UDim2.new(1,0,0,44)
                nc.BackgroundColor3=HC.Surface2; nc.BorderSizePixel=0; nc.LayoutOrder=1; corner(nc,8)
                local nl=lbl(nc,"Nenhum amigo online no momento. 😴",11,nil,HC.TextMuted,1)
                nl.Size=UDim2.new(1,0,1,0); nl.TextXAlignment=Enum.TextXAlignment.Center
                return
            end
            for i,f in ipairs(onlineFriends) do
                local isInGame=(f.userPresenceType==2)
                local isPrivate=isInGame and (not f.gameId or f.gameId=="" or f.gameId==nil)
                local isSameGame=isInGame and (f.placeId==game.PlaceId or f.rootPlaceId==game.PlaceId)

                local cardH=isInGame and 78 or 62
                local card=Instance.new("Frame",aFrame); card.Size=UDim2.new(1,0,0,cardH)
                card.BackgroundColor3=HC.Surface2; card.BorderSizePixel=0; card.LayoutOrder=i; corner(card,8)

                local avatarF=Instance.new("Frame",card); avatarF.Size=UDim2.new(0,48,0,48)
                avatarF.Position=UDim2.new(0,8,0.5,-24); avatarF.BackgroundColor3=HC.Surface; avatarF.BorderSizePixel=0; corner(avatarF,6)
                local avatarImg=Instance.new("ImageLabel",avatarF); avatarImg.Size=UDim2.new(1,0,1,0)
                avatarImg.BackgroundTransparency=1; corner(avatarImg,6)
                task.spawn(function()
                    local ok3,img=pcall(function() return Players:GetUserThumbnailAsync(f.userId,Enum.ThumbnailType.HeadShot,Enum.ThumbnailSize.Size48x48) end)
                    if ok3 and avatarImg.Parent then avatarImg.Image=img end
                end)
                local onDot=Instance.new("Frame",avatarF); onDot.Size=UDim2.new(0,13,0,13)
                onDot.AnchorPoint=Vector2.new(1,1); onDot.Position=UDim2.new(1,3,1,3)
                onDot.BackgroundColor3=isInGame and HC.Success or HC.Info; onDot.BorderSizePixel=0; corner(onDot,99)
                local dotStroke=Instance.new("UIStroke",onDot); dotStroke.Color=HC.Background; dotStroke.Thickness=2; dotStroke.Transparency=0

                local nameLbl=lbl(card,f.name or "Desconhecido",12,Enum.Font.GothamBold,HC.Text,1)
                nameLbl.Size=UDim2.new(1,-195,0,16); nameLbl.Position=UDim2.new(0,64,0,10)
                nameLbl.TextXAlignment=Enum.TextXAlignment.Left
                if f.displayName and f.displayName~=f.name then
                    local dnLbl=lbl(card,"@"..f.displayName,9,nil,HC.TextMuted,1)
                    dnLbl.Size=UDim2.new(1,-195,0,12); dnLbl.Position=UDim2.new(0,64,0,27)
                    dnLbl.TextXAlignment=Enum.TextXAlignment.Left
                end

                local statusText, statusColor
                if isInGame then
                    if isPrivate then
                        statusText="🔒  Servidor Privado"
                        statusColor=Color3.fromRGB(255,180,60)
                    else
                        statusText="🎮  "..(f.lastLocation or "Jogo desconhecido")
                        statusColor=HC.Success
                    end
                else
                    statusText="🏠  Menu Principal / Website"
                    statusColor=HC.TextMuted
                end
                local statusLbl2=lbl(card,statusText,10,Enum.Font.Gotham,statusColor,1)
                local statusY=(f.displayName and f.displayName~=f.name) and 40 or 28
                statusLbl2.Size=UDim2.new(1,-195,0,14); statusLbl2.Position=UDim2.new(0,64,0,statusY)
                statusLbl2.TextXAlignment=Enum.TextXAlignment.Left

                if isInGame and f.universeId and f.universeId~=0 then
                    local thumbF=Instance.new("Frame",card); thumbF.Size=UDim2.new(0,44,0,44)
                    thumbF.Position=UDim2.new(1,-134,0.5,-22); thumbF.BackgroundColor3=HC.Surface; thumbF.BorderSizePixel=0; corner(thumbF,6)
                    stroke(thumbF,HC.Border,0.6)
                    local thumbImg=Instance.new("ImageLabel",thumbF); thumbImg.Size=UDim2.new(1,0,1,0)
                    thumbImg.BackgroundTransparency=1; corner(thumbImg,6); thumbImg.Image=""
                    getGameThumb(f.universeId,thumbImg)
                end

                local canFollow=isInGame and not isPrivate
                local followBtn=Instance.new("TextButton",card); followBtn.Size=UDim2.new(0,78,0,28)
                followBtn.AnchorPoint=Vector2.new(1,0.5); followBtn.Position=UDim2.new(1,-8,0.5,0)
                followBtn.BackgroundColor3=canFollow and HC.Accent or HC.Border
                followBtn.BorderSizePixel=0
                followBtn.Text=isInGame and (isPrivate and "🔒 Privado" or "Seguir") or "Offline"
                followBtn.TextColor3=Color3.new(1,1,1); followBtn.TextSize=10; followBtn.Font=Enum.Font.GothamBold
                followBtn.ZIndex=5; corner(followBtn,6)
                followBtn.MouseButton1Click:Connect(function()
                    pC()
                    if not isInGame then
                        showTopNotif(f.name.." nao esta em nenhum jogo",HC.Border); return
                    end
                    if isPrivate then
                        showTopNotif("🔒  Servidor privado — impossivel seguir "..f.name,HC.Danger); return
                    end
                    if isSameGame and f.gameId and #tostring(f.gameId)>5 then
                        showTopNotif("Teleportando para o servidor de "..f.name.."...",HC.Accent)
                        task.delay(1.5,function()
                            pcall(function()
                                TeleportService:TeleportToPlaceInstance(f.placeId,f.gameId,LocalPlayer)
                            end)
                        end)
                    else
                        showTopNotif("Seguindo "..f.name.." para "..(f.lastLocation or "jogo").."...",HC.Accent)
                        task.delay(1.5,function()
                            pcall(function()
                                TeleportService:Teleport(f.rootPlaceId or f.placeId,LocalPlayer)
                            end)
                        end)
                    end
                end)
                card.MouseEnter:Connect(function() tw(card,{BackgroundColor3=Color3.fromRGB(37,37,55)},0.1):Play() end)
                card.MouseLeave:Connect(function() tw(card,{BackgroundColor3=HC.Surface2},0.1):Play() end)
            end
        end)
    end
    aRefBtn.MouseButton1Click:Connect(function() pC(); refreshAmigos(); showTopNotif("Buscando amigos online...",HC.Info) end)
    task.spawn(refreshAmigos)
end
_buildAmigosTab()

-- ============================================================
-- ABA TP
-- ============================================================
local function _buildTPTab()
    currentTab="TP"; local tpPag=Pages["TP"]
    criarHeader(tpPag,"[ TELEPORTES ]",1)
    criarToggle(tpPag,"Click TP","LCtrl + Clique para teleportar","Segure LCtrl e clique em qualquer superficie.",false,
        function() clickTPAtivo=true; showTopNotif("Click TP ativado - LCtrl+Clique",HC.Accent) end,
        function() clickTPAtivo=false; showTopNotif("Click TP desativado",HC.Border) end,2)
    criarHeader(tpPag,"[ SALVAR POSICAO ]",4)
    local dica=Instance.new("Frame",tpPag); dica.Size=UDim2.new(1,0,0,28); dica.LayoutOrder=4
    dica.BackgroundColor3=Color3.fromRGB(18,28,40); dica.BorderSizePixel=0; corner(dica,6)
    stroke(dica,HC.Info,0.6)
    local dicaL=lbl(dica,"✏  O titulo de cada save pode ser alterado diretamente!",9,Enum.Font.GothamSemibold,Color3.fromRGB(100,170,255),1)
    dicaL.Size=UDim2.new(1,-12,1,0); dicaL.Position=UDim2.new(0,8,0,0); dicaL.TextXAlignment=Enum.TextXAlignment.Left
    local tpSaveList=Instance.new("Frame",tpPag); tpSaveList.Size=UDim2.new(1,0,0,0)
    tpSaveList.BackgroundTransparency=1; tpSaveList.AutomaticSize=Enum.AutomaticSize.Y; tpSaveList.LayoutOrder=5
    local tpSL=Instance.new("UIListLayout",tpSaveList); tpSL.Padding=UDim.new(0,6); tpSL.SortOrder=Enum.SortOrder.LayoutOrder
    local _,saveBtn=criarBotao(tpPag,"Salvar Posicao Atual","Maximo de 8 saves","Salva a posicao atual.",
        function()
            if #tpSaves>=8 then showTopNotif("Limite de 8 saves!",HC.Danger); return end
            local ok,root=pcall(getHRP); if not ok then return end
            local pos=root.Position; local idx=#tpSaves+1; table.insert(tpSaves,pos)
            local sCard=Instance.new("Frame",tpSaveList); sCard.Size=UDim2.new(1,0,0,58)
            sCard.BackgroundColor3=HC.Surface2; sCard.BorderSizePixel=0; sCard.LayoutOrder=idx; corner(sCard,8)
            local editHint=lbl(sCard,"✏",11,Enum.Font.GothamBold,HC.TextMuted,1)
            editHint.Size=UDim2.new(0,18,0,18); editHint.Position=UDim2.new(0,8,0,6)
            editHint.TextXAlignment=Enum.TextXAlignment.Center
            local sTitleBox=Instance.new("TextBox",sCard)
            sTitleBox.Size=UDim2.new(1,-34,0,20); sTitleBox.Position=UDim2.new(0,28,0,4)
            sTitleBox.BackgroundColor3=Color3.fromRGB(20,20,36); sTitleBox.BorderSizePixel=0
            sTitleBox.Text="Save "..idx; sTitleBox.TextSize=12; sTitleBox.Font=Enum.Font.GothamBold
            sTitleBox.TextColor3=HC.Text; sTitleBox.TextXAlignment=Enum.TextXAlignment.Left
            sTitleBox.PlaceholderText="Clique para renomear..."; sTitleBox.PlaceholderColor3=HC.TextMuted
            sTitleBox.ClearTextOnFocus=false; sTitleBox.ZIndex=5; corner(sTitleBox,4); pad(sTitleBox,4,4,0,0)
            stroke(sTitleBox,HC.Accent,0.7)
            sTitleBox.Focused:Connect(function() tw(sTitleBox,{BackgroundColor3=Color3.fromRGB(30,18,55)},0.12):Play() end)
            sTitleBox.FocusLost:Connect(function() tw(sTitleBox,{BackgroundColor3=Color3.fromRGB(20,20,36)},0.12):Play() end)
            local sPosLbl=lbl(sCard,string.format("%.0f, %.0f, %.0f",pos.X,pos.Y,pos.Z),9,nil,HC.TextMuted,1)
            sPosLbl.Size=UDim2.new(1,-20,0,13); sPosLbl.Position=UDim2.new(0,10,0,27); sPosLbl.TextXAlignment=Enum.TextXAlignment.Left
            local tpBtnS=Instance.new("TextButton",sCard); tpBtnS.Size=UDim2.new(0,80,0,20)
            tpBtnS.AnchorPoint=Vector2.new(1,1); tpBtnS.Position=UDim2.new(1,-8,1,-6)
            tpBtnS.BackgroundColor3=HC.Accent; tpBtnS.BorderSizePixel=0; tpBtnS.Text="Teleportar"
            tpBtnS.TextColor3=Color3.new(1,1,1); tpBtnS.TextSize=10; tpBtnS.Font=Enum.Font.GothamBold; tpBtnS.ZIndex=5; corner(tpBtnS,5)
            tpBtnS.MouseButton1Click:Connect(function()
                pC(); local ok2,r=pcall(getHRP); if not ok2 then return end
                r.CFrame=CFrame.new(pos); showTopNotif("TP -> "..sTitleBox.Text,HC.Accent)
            end)
            local delBtnS=Instance.new("TextButton",sCard); delBtnS.Size=UDim2.new(0,56,0,20)
            delBtnS.AnchorPoint=Vector2.new(1,1); delBtnS.Position=UDim2.new(1,-96,1,-6)
            delBtnS.BackgroundColor3=HC.Danger; delBtnS.BorderSizePixel=0; delBtnS.Text="Deletar"
            delBtnS.TextColor3=Color3.new(1,1,1); delBtnS.TextSize=10; delBtnS.Font=Enum.Font.GothamBold; delBtnS.ZIndex=5; corner(delBtnS,5)
            delBtnS.MouseButton1Click:Connect(function() pC(); sCard:Destroy(); table.remove(tpSaves,idx) end)
            sCard.MouseEnter:Connect(function() tw(sCard,{BackgroundColor3=Color3.fromRGB(37,37,55)},0.1):Play() end)
            sCard.MouseLeave:Connect(function() tw(sCard,{BackgroundColor3=HC.Surface2},0.1):Play() end)
            showTopNotif("Posicao "..idx.." salva!",HC.Success)
        end,3)
    saveBtn.Text="Salvar"
end
_buildTPTab()

-- ============================================================
-- ABA MISC
-- ============================================================
local function _buildMiscTab()
    currentTab="Misc"; local miscPag=Pages["Misc"]
    criarHeader(miscPag,"[ INTERFACE ]",1)
    criarToggle(miscPag,"Travar Janela","Impede arrastar e redimensionar","Trava posicao e tamanho do hub.",false,
        function() janelaTravada=true end, function() janelaTravada=false end,2)
    criarToggle(miscPag,"Bolinha ao Minimizar","Ative para mostrar bolinha arrastavel ao minimizar",
        "Quando ativo, ao minimizar aparece a bolinha. Duplo clique nela para reabrir.",false,
        function() useBall=true end, function() useBall=false end,3)

    criarHeader(miscPag,"[ DISPLAY ]",4)
    do
        local fpsCard,fpsBar=baseCard(miscPag,50,5); local fpsEstado=false
        local fpsPill,_,setFpsPill=criarPill(fpsCard,false)
        local fpsTRow,_=makeTitleRow(fpsCard,"Mostrar FPS",56)
        criarInfoBtn(fpsTRow,"FPS no centro superior. Duplo clique no FPS para modo arrastar.")
        local fpsSLbl=lbl(fpsCard,"Centro superior | 2x clique = arrastar",10,nil,HC.TextMuted,1)
        fpsSLbl.Size=UDim2.new(1,-64,0,14); fpsSLbl.Position=UDim2.new(0,12,0,30); fpsSLbl.TextXAlignment=Enum.TextXAlignment.Left
        fpsBar.BackgroundTransparency=1
        local fpsBtnL=Instance.new("TextButton",fpsCard); fpsBtnL.Size=UDim2.new(1,0,0,50); fpsBtnL.BackgroundTransparency=1; fpsBtnL.Text=""; fpsBtnL.ZIndex=1
        fpsBtnL.MouseButton1Click:Connect(function()
            pT(); fpsEstado=not fpsEstado; setFpsPill(fpsEstado); fpsAtivo=fpsEstado
            tw(fpsBar,{BackgroundTransparency=fpsEstado and 0 or 1},0.15):Play()
            FpsDisplay.Visible=fpsEstado
            if fpsEstado then
                if fpsConn then fpsConn:Disconnect() end
                local t2=0; local fr=0
                fpsConn=RunService.RenderStepped:Connect(function(dt)
                    if _G.BZSession~=BZ_SID then fpsConn:Disconnect(); return end
                    fr=fr+1; t2=t2+dt
                    if t2>=0.25 then
                        local fps=math.round(fr/t2); FpsValueLbl.Text=tostring(fps)
                        FpsDot.BackgroundColor3=fps>=55 and HC.Success or fps>=30 and Color3.fromRGB(255,200,0) or HC.Danger
                        t2=0; fr=0
                    end
                end)
                showTopNotif("FPS ativado — centro superior da tela",HC.Success)
            else
                if fpsConn then fpsConn:Disconnect(); fpsConn=nil end
                FpsValueLbl.Text="--"
            end
        end)
        table.insert(allToggleSetters,{fn=function()
            fpsEstado=false; setFpsPill(false); fpsAtivo=false; fpsBar.BackgroundTransparency=1
            FpsDisplay.Visible=false
            if fpsConn then fpsConn:Disconnect(); fpsConn=nil end; FpsValueLbl.Text="--"
        end})
        table.insert(allCards,{card=fpsCard,tab="Misc",name="mostrar fps",keywords="fps frames tela counter",origParent=miscPag})
    end

    criarHeader(miscPag,"[ SISTEMA ]",10)
    criarBotao(miscPag,"Rejoin","Reconectar ao mesmo servidor","Recarrega no mesmo servidor atual.",
        function()
            showTopNotif("Reconectando ao servidor...",HC.Info)
            task.delay(2.2,function()
                pcall(function()
                    TeleportService:TeleportToPlaceInstance(game.PlaceId,game.JobId,LocalPlayer)
                end)
            end)
        end,11)
    criarBotao(miscPag,"Reiniciar Hub","Desfaz tudo, se destroi e reabre do zero","Reseta todos os estados e reinicia o script.",
        function()
            showTopNotif("Reiniciando hub...",HC.Info)
            task.delay(0.6,function()
                for _,setter in ipairs(allToggleSetters) do pcall(setter.fn) end
                if flyAtivo    then flyAtivo=false;    pcall(stopFly)      end
                if noclipAtivo then noclipAtivo=false; pcall(toggleNoclip) end
                if invisAtivo  then invisAtivo=true;   pcall(toggleInvis)  end
                if freecamAtivo then pcall(toggleFreecam) end
                if spectateTarget then pcall(stopSpectate) end
                if tlModoAtivo then tlClearAll() end
                if flingAtivo then flingAtivo=false; workspace.FallenPartsDestroyHeight=flingFPDH end
                superPuloAtivo=false; aimAtivo=false; clickTPAtivo=false
                aimLockedTarget=nil; aimLastPos=nil
                kbGated={fly=false,invis=false,jump=false,freecam=false,noclip=false,fling=false}
                pcall(function()
                    Camera.CameraType=Enum.CameraType.Custom; Camera.FieldOfView=70
                    local myChar=LocalPlayer.Character
                    if myChar then
                        local h=myChar:FindFirstChildOfClass("Humanoid")
                        if h then Camera.CameraSubject=h end
                    end
                end)
                pcall(function() espFolder:Destroy() end)
                pcall(function() tlSGui:Destroy() end)
                local inv=workspace:FindFirstChild("_BZinvischair")
                if inv then pcall(function() inv:Destroy() end) end
                task.wait(0.1)
                pcall(function() ScreenGui:Destroy() end)
                if _G.BEZALEL_MAIN then _G.BEZALEL_MAIN() end
            end)
        end,12)
end
_buildMiscTab()

-- ============================================================
-- ABA CREDITOS
-- ============================================================
local function _buildCreditosTab()
    currentTab="Creditos"; local cPag=Pages["Creditos"]
    local titleCard=Instance.new("Frame",cPag); titleCard.Size=UDim2.new(1,0,0,70); titleCard.LayoutOrder=1
    titleCard.BackgroundColor3=HC.Accent; titleCard.BorderSizePixel=0; corner(titleCard,10)
    local tGrad=Instance.new("UIGradient",titleCard)
    tGrad.Color=ColorSequence.new{ColorSequenceKeypoint.new(0,HC.Accent),ColorSequenceKeypoint.new(1,HC.AccentDark)}
    tGrad.Rotation=135
    local ct1=lbl(titleCard,"Bezalel Hub",18,Enum.Font.GothamBold,Color3.new(1,1,1),1)
    ct1.Size=UDim2.new(1,0,0,28); ct1.Position=UDim2.new(0,0,0,12); ct1.TextXAlignment=Enum.TextXAlignment.Center
    local ct2=lbl(titleCard,"Versao 1.0  —  Em Desenvolvimento",10,Enum.Font.Gotham,Color3.fromRGB(220,190,255),1)
    ct2.Size=UDim2.new(1,0,0,16); ct2.Position=UDim2.new(0,0,0,42); ct2.TextXAlignment=Enum.TextXAlignment.Center
    local devCard=Instance.new("Frame",cPag); devCard.Size=UDim2.new(1,0,0,80); devCard.LayoutOrder=2
    devCard.BackgroundColor3=HC.Surface2; devCard.BorderSizePixel=0; corner(devCard,8)
    local devT=lbl(devCard,"Desenvolvedor",9,Enum.Font.GothamBold,HC.TextMuted,1)
    devT.Size=UDim2.new(1,-16,0,14); devT.Position=UDim2.new(0,12,0,8); devT.TextXAlignment=Enum.TextXAlignment.Left
    local devName=lbl(devCard,"Bezalel",16,Enum.Font.GothamBold,HC.Accent,1)
    devName.Size=UDim2.new(1,-16,0,24); devName.Position=UDim2.new(0,12,0,24); devName.TextXAlignment=Enum.TextXAlignment.Left
    local devSub=lbl(devCard,"Design, codigo e todas as funcionalidades",10,Enum.Font.Gotham,HC.TextMuted,1)
    devSub.Size=UDim2.new(1,-16,0,14); devSub.Position=UDim2.new(0,12,0,50); devSub.TextXAlignment=Enum.TextXAlignment.Left
    local msgCard=Instance.new("Frame",cPag); msgCard.Size=UDim2.new(1,0,0,0); msgCard.LayoutOrder=3
    msgCard.BackgroundColor3=HC.Surface2; msgCard.BorderSizePixel=0; msgCard.AutomaticSize=Enum.AutomaticSize.Y; corner(msgCard,8)
    local msgTxt=lbl(msgCard,
        "Oi! Obrigado por usar o Bezalel Hub.\n\n"..
        "Esse e meu primeiro script hub — desenvolvi tudo do zero, sozinho: o design, o layout, cada funcionalidade e cada detalhe visual.\n\n"..
        "O script ainda esta em desenvolvimento ativo, entao e possivel que voce encontre alguns bugs por ai. Se encontrar, nao se preocupe, estou sempre trabalhando para melhorar!\n\n"..
        "Em breve terei muito mais funcionalidades, incluindo o AutoFarm completo. Fique ligado!",
        10,Enum.Font.Gotham,HC.Text,1)
    msgTxt.Size=UDim2.new(1,-24,0,0); msgTxt.Position=UDim2.new(0,12,0,12)
    msgTxt.AutomaticSize=Enum.AutomaticSize.Y; msgTxt.TextWrapped=true
    msgTxt.TextXAlignment=Enum.TextXAlignment.Left; msgTxt.TextYAlignment=Enum.TextYAlignment.Top
    msgTxt.LineHeight=1.4
    local msgPad=Instance.new("UIPadding",msgCard); msgPad.PaddingBottom=UDim.new(0,14)
end
_buildCreditosTab()

-- ============================================================
-- INPUT HANDLER
-- ============================================================
UserInputService.InputBegan:Connect(function(input,gameProcessed)
    if input.KeyCode==KB.hub then if toggleHub then toggleHub() end; return end

    if kbEscutando then
        if input.UserInputType==Enum.UserInputType.Keyboard then
            kbEscutando=false
            if kbCb then kbCb(input.KeyCode==Enum.KeyCode.Escape and Enum.KeyCode.Unknown or input.KeyCode); kbCb=nil end
        end
        return
    end

    -- RMB press — aimbot one-shot scan
    if input.UserInputType==Enum.UserInputType.MouseButton2 then
        if aimAtivo then
            local best,bestA=nil,aimFOV
            for _,p in pairs(Players:GetPlayers()) do
                if p~=LocalPlayer and p.Character then
                    local part=aimGetPart(p.Character); if not part then continue end
                    local dir=(part.Position-Camera.CFrame.Position).Unit
                    local a=math.deg(math.acos(math.clamp(Camera.CFrame.LookVector:Dot(dir),-1,1)))
                    if a<bestA then bestA=a; best=p end
                end
            end
            aimLockedTarget=best
            aimLastPos=nil
        end
        if tlAtivo and tlModoAtivo then tlRmbHeld=true end
        return
    end

    if gameProcessed then return end

    -- FIX 11: Se Z estiver pressionado (transformacao), ignora teclas do hub
    if UserInputService:IsKeyDown(Enum.KeyCode.Z) then return end

    local key=input.KeyCode
    if key~=Enum.KeyCode.Unknown then
        local msgs={}; local lastOn=false

        if key==KB.fly and kbGated.fly then
            flyAtivo=not flyAtivo
            if flyAtivo then startFly() else stopFly() end
            table.insert(msgs,"Voar "..(flyAtivo and "ON" or "OFF")); lastOn=flyAtivo
        end
        if key==KB.invis and kbGated.invis then
            toggleInvis()
            table.insert(msgs,"Invisivel "..(invisAtivo and "ON" or "OFF")); lastOn=invisAtivo
        end
        if key==KB.jump and kbGated.jump then
            superPuloAtivo=not superPuloAtivo
            table.insert(msgs,"Super Pulo "..(superPuloAtivo and "ON" or "OFF")); lastOn=superPuloAtivo
        end
        if key==KB.freecam and kbGated.freecam and not freecamAtivo then
            toggleFreecam()
            table.insert(msgs,"Camera Livre ON"); lastOn=true
        end
        if key==KB.noclip and kbGated.noclip then
            toggleNoclip()
            table.insert(msgs,"Noclip "..(noclipAtivo and "ON" or "OFF")); lastOn=noclipAtivo
        end
        if key==KB.vision then
            task.spawn(ativarVisao)
            table.insert(msgs,"Visao Sensorial ativada"); lastOn=true
        end
        if key==KB.tl then
            tlModoAtivo=not tlModoAtivo; tlSetMode(tlModoAtivo)
            if not tlModoAtivo then tlClearAll() end
            table.insert(msgs,"Passo Fantasma "..(tlModoAtivo and "ON" or "OFF")); lastOn=tlModoAtivo
        end
        if key==KB.fling and kbGated.fling then
            flingAtivo=not flingAtivo
            if not flingAtivo then workspace.FallenPartsDestroyHeight=flingFPDH end
            table.insert(msgs,"Proximity Fling "..(flingAtivo and "ON — Raio: "..flingRadius.."st" or "OFF"))
            lastOn=flingAtivo
        end

        if #msgs>0 then
            showTopNotif(table.concat(msgs," + "),lastOn and HC.Accent or HC.Border)
        end
    end

    if input.UserInputType==Enum.UserInputType.MouseButton1 then
        if tlAtivo and tlModoAtivo and tlRmbHeld and tlHovered then
            local char=tlHovered.Character; if not char then return end
            local root2=char:FindFirstChild("HumanoidRootPart"); if not root2 then return end
            local ok,myRoot=pcall(getHRP); if not ok then return end
            local dist=(root2.Position-myRoot.Position).Magnitude
            if dist>maxTlDist then
                showTopNotif("Passo Fantasma: muito longe! ("..math.floor(dist).."st > "..maxTlDist.."st max)",HC.Danger)
                tlClearAll()
                return
            end
            pcall(function() tlSnd:Play() end)
            myRoot.CFrame=root2.CFrame*CFrame.new(0,0,3)
            showTopNotif("TP -> "..tlHovered.Name.." | Passo Fantasma desativado",HC.Accent)
            tlClearAll()
            return
        end
        if clickTPAtivo and UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then
            local mouse=LocalPlayer:GetMouse()
            if mouse.Target then
                local pos=mouse.Hit.Position+Vector3.new(0,3,0)
                local ok,root=pcall(getHRP); if not ok then return end
                root.CFrame=CFrame.new(pos); showTopNotif("Click TP",HC.Accent)
            end
        end
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType==Enum.UserInputType.MouseButton2 then
        tlRmbHeld=false
        if aimAtivo then aimLockedTarget=nil; aimLastPos=nil end
    end
    if input.UserInputType==Enum.UserInputType.MouseButton1 then activeDrag=nil end
end)

-- Drag handler (sliders)
RunService.RenderStepped:Connect(function()
    if _G.BZSession~=BZ_SID then return end
    if not activeDrag then return end
    if not UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton1) then activeDrag=nil; return end
    local mouse=UserInputService:GetMouseLocation()
    local track=activeDrag.track
    if not track or not track.Parent then activeDrag=nil; return end
    local abs=track.AbsolutePosition; local w=track.AbsoluteSize.X
    local ratio=math.clamp((mouse.X-abs.X)/w,0,1)
    if activeDrag.isRaw then
        if activeDrag.cb then activeDrag.cb(ratio) end
    else
        local minV=activeDrag.minV; local maxV=activeDrag.maxV
        local val=math.round(minV+(maxV-minV)*ratio)
        if activeDrag.fill then activeDrag.fill.Size=UDim2.new(ratio,0,1,0) end
        if activeDrag.mark then activeDrag.mark.Position=UDim2.new(ratio,0,0.5,0) end
        if activeDrag.valLbl then
            local txt=activeDrag.valLbl.Text
            activeDrag.valLbl.Text=txt:find("Altura") and "Altura:  "..val or tostring(val)
        end
        if activeDrag.cb then activeDrag.cb(val) end
    end
end)

-- HUB SHOW/HIDE
hubVisible=true
mostrarHub=function()
    hubVisible=true; Main.Visible=true; Main.Size=UDim2.new(0,0,0,0)
    tw(Main,{Size=UDim2.new(0,hubW,0,hubH)},0.28,Enum.EasingStyle.Back):Play(); pO()
    MiniBall.Visible=false
end
esconderHub=function()
    hubVisible=false
    tw(Main,{Size=UDim2.new(0,hubW,0,0)},0.2):Play()
    task.delay(0.25,function()
        if not hubVisible then
            Main.Visible=false
            if useBall then
                MiniBall.Visible=true
                tw(MiniBall,{Size=UDim2.new(0,72,0,72)},0.25,Enum.EasingStyle.Back):Play()
            end
        end
    end)
end
toggleHub=function() if hubVisible then esconderHub() else mostrarHub() end end

BtnClose.MouseButton1Click:Connect(function() pC(); esconderHub() end)
BtnMin.MouseButton1Click:Connect(function()
    pC(); esconderHub()
    task.delay(0.4,function()
        if not useBall then showTopNotif("Hub minimizado — pressione Home para abrir",HC.Info) end
    end)
end)

-- Arrastar janela
do
    local dragging=false; local dragStart; local startPos
    TopBar.InputBegan:Connect(function(input)
        if janelaTravada then return end
        if input.UserInputType==Enum.UserInputType.MouseButton1 then
            dragging=true; dragStart=input.Position; startPos=Main.Position
        end
    end)
    TopBar.InputEnded:Connect(function(input)
        if input.UserInputType==Enum.UserInputType.MouseButton1 then dragging=false end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if dragging and input.UserInputType==Enum.UserInputType.MouseMovement then
            local delta=input.Position-dragStart
            Main.Position=UDim2.new(startPos.X.Scale,startPos.X.Offset+delta.X,startPos.Y.Scale,startPos.Y.Offset+delta.Y)
        end
    end)
end

-- Resize
do
    local resizing=false; local rStart; local rSize
    ResizeHandle.MouseButton1Down:Connect(function()
        if janelaTravada then return end
        resizing=true; rStart=UserInputService:GetMouseLocation()
        rSize=Vector2.new(Main.Size.X.Offset,Main.Size.Y.Offset)
    end)
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType==Enum.UserInputType.MouseButton1 then resizing=false end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if resizing and input.UserInputType==Enum.UserInputType.MouseMovement then
            local m=UserInputService:GetMouseLocation(); local delta=m-rStart
            hubW=math.clamp(rSize.X+delta.X,420,950); hubH=math.clamp(rSize.Y+delta.Y,300,720)
            Main.Size=UDim2.new(0,hubW,0,hubH)
        end
    end)
end

-- Pesquisa
SearchInput:GetPropertyChangedSignal("Text"):Connect(function()
    local q=string.lower(SearchInput.Text)
    if q=="" then
        SearchResultsPage.Visible=false
        for _,card in ipairs(allCards) do
            if card.origParent and card.card.Parent==SearchResultsPage then
                card.card.Parent=card.origParent
            end
        end
        for n,p in pairs(Pages) do p.Visible=(n==ActiveTab) end; return
    end
    SearchResultsPage.Visible=true
    for _,p in pairs(Pages) do p.Visible=false end
    for _,card in ipairs(allCards) do
        if card.name:find(q,1,true) or card.keywords:find(q,1,true) then
            card.card.Parent=SearchResultsPage
        else
            if card.origParent and card.card.Parent==SearchResultsPage then
                card.card.Parent=card.origParent
            end
        end
    end
end)

trocarAba("Visual")
Main.Size=UDim2.new(0,0,0,0)
tw(Main,{Size=UDim2.new(0,hubW,0,hubH)},0.35,Enum.EasingStyle.Back):Play(); pO()

-- Fade-in gradual das abas apos o hub abrir
task.spawn(function()
    task.wait(0.55)
    for _,tabName in ipairs(TabNames) do
        local page=Pages[tabName]; if not page then continue end
        local children=page:GetChildren()
        for _,child in ipairs(children) do
            if child:IsA("Frame") and child.BackgroundTransparency<0.9 then
                local origT=child.BackgroundTransparency
                child.BackgroundTransparency=1
                tw(child,{BackgroundTransparency=origT},0.18,Enum.EasingStyle.Quad):Play()
                task.wait(0.025)
            end
        end
    end
end)

end -- BEZALEL_MAIN

_G.BEZALEL_MAIN=BEZALEL_MAIN

if not _G.BZFirstLoad then
    _G.BZFirstLoad=true
    mostrarLoader(BEZALEL_MAIN)
else
    BEZALEL_MAIN()
end
