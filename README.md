-- // bypass \\ --

 

repeat

    wait()

  until game:IsLoaded()

  local OldNameCall = nil

  OldNameCall = hookmetamethod(game, "__namecall", function(...)

    local Self, Key = ...

    if (Self.Name == "" or Self.Name == "RemoteEvent") and Self.Parent.Name == "Security" and Self.ClassName == "RemoteEvent" then

        return wait(9e9)

    end

    return OldNameCall(...)

  end)

  

  -- // values, etc \\

  

  local RunService, UserInputService, TweenService = game.RunService, game.UserInputService, game.TweenService

  local Player, ReplicatedStorage, Debris = game.Players.LocalPlayer, game.ReplicatedStorage, game.Debris

    

  local Remotes = {

      ThunderDash = ReplicatedStorage.Remotes.ThunderDash, -- // ThunderDash Endpoint, Table where parts are put (just put random cframes) FIRETYPE = :FireServer()

      Emote = ReplicatedStorage.Remotes.CustomEmote, -- // Boolean / true or false, Emote / 'Emperyean or sumn' and 'Wavelight' FIRETYPE = :FireServer()

      Parry = ReplicatedStorage.Remotes.ParryButtonPress -- // nil / no tuple FIRETYPE = :Fire()

  }

    

  local bug_ball_method_____________________________________init = false

  local ParryCD = false

  local Parry = false

  local Visual = false

  

  -- // hitbox \\ --

    

  local radius = Instance.new('Part', workspace)

  radius.Color = Color3.fromHex('#1EFF00')

  radius.Anchored = true

  radius.Material = Enum.Material.ForceField

  radius.Shape = Enum.PartType.Ball

  radius.CanCollide = false

  radius.CastShadow = false

  

  -- // loading the ui \\ --

  

  local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()

  local SaveManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/SaveManager.lua"))()

  local InterfaceManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/InterfaceManager.lua"))()

  

  local Window = Fluent:CreateWindow({

      Title = "Vision",

      SubTitle = "By Forex",

      TabWidth = 160,

      Size = UDim2.fromOffset(580, 460),

      Acrylic = false,

      Theme = "Dark",

      MinimizeKey = Enum.KeyCode.LeftControl

  })

  

  local Tabs = {

      Combat = Window:AddTab({ Title = "Combat", Icon = "swords" }),

      Misc = Window:AddTab({ Title = "Misc", Icon = "package-plus" }),

      Settings = Window:AddTab({ Title = "Settings", Icon = "settings" })

  }

  

  local ToggleAutoParry = Tabs.Combat:AddToggle("ToggleAutoParry", {

    Title = "Auto Parry",

    Default = false,

    Callback = function(Value)

        Parry = Value

    end

  })

  

  local ToggleVisual = Tabs.Combat:AddToggle("ToggleVisual", {

    Title = "Show Distance Visualizer",

    Default = false,

    Callback = function(Value)

      Visual = Value

    end

  })

  

  local ToggleBB = Tabs.Combat:AddToggle("ToggleBB", {

    Title = "Bug Ball",

    Default = false,

    Callback = function(Value)

      bug_ball_method_____________________________________init = value

    end

  })

  

  local ButtonAntiAfk = Tabs.Misc:AddButton({

    Title = "Parry Spam",

    Default = false,

    Callback = function(Value)

      local ballService = workspace:WaitForChild("Balls")

      local userInputService = game:GetService("UserInputService")

      local replicatedStorage = game:GetService("ReplicatedStorage")

      local runService = game:GetService("RunService")

      local stats = game:GetService("Stats")

      local workspaceStats = stats.Workspace

      local networkStats = stats.Network.ServerStatsItem

      

      local gravity = Vector3.new(0, -9.81, 0)

      local mass = 1

      local force = gravity * mass

      

      local isSpamming = false

      local player = game.Players.LocalPlayer

      local screenGui = Instance.new("ScreenGui")

      screenGui.ResetOnSpawn = false

      screenGui.Parent = player:WaitForChild("PlayerGui")

      

      local frame = Instance.new("Frame")

      frame.Size = UDim2.new(0, 250, 0, 70)

      frame.Position = UDim2.new(1, -280, 1, -70)

      frame.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)

      frame.BackgroundTransparency = 0.5

      frame.BorderSizePixel = 0

      frame.Parent = screenGui

      

      frame.ClipsDescendants = true

      frame:TweenSize(UDim2.new(0, 250, 0, 70), Enum.EasingDirection.Out, Enum.EasingStyle.Quint, 0.5, true)

      

      local button = Instance.new("TextButton")

      button.Text = "Start Spamming"

      button.Size = UDim2.new(0.8, 0, 0.31, 0)

      button.Position = UDim2.new(0.1, 0, 0.1, 0)

      button.Parent = frame

      button.BackgroundColor3 = Color3.new(0.2, 0.6, 0.2)

      button.TextColor3 = Color3.new(1, 1, 1)

      button.Font = Enum.Font.GothamBold

      button.TextSize = 20

      button.TextWrapped = true

      

      local labels = {}

      

      local pingLabel = Instance.new("TextLabel")

      pingLabel.Size = UDim2.new(0, 100, 0, 20)

      pingLabel.Position = UDim2.new(0.3, 0, 0.45, 0)

      pingLabel.Parent = frame

      pingLabel.BackgroundColor3 = Color3.new(0, 0, 0)

      pingLabel.TextColor3 = Color3.new(1, 1, 1)

      pingLabel.Font = Enum.Font.GothamBold

      pingLabel.TextSize = 16

      labels.Ping = pingLabel

      

      local fpsLabel = Instance.new("TextLabel")

      fpsLabel.Size = UDim2.new(0, 100, 0, 20)

      fpsLabel.Position = UDim2.new(0.3, 0, 0.7, 0)

      fpsLabel.Parent = frame

      fpsLabel.BackgroundColor3 = Color3.new(0, 0, 0)

      fpsLabel.TextColor3 = Color3.new(1, 1, 1)

      fpsLabel.Font = Enum.Font.GothamBold

      fpsLabel.TextSize = 16

      labels.FPS = fpsLabel

      

      local spammingConnection

      

      local lastSpamTime = 0

      local spamInterval = 0

      

      local function Parry()

        replicatedStorage.Remotes.ParryButtonPress:Fire()

      end

      

      local function Parry2()

        local parryData = {

          ["5116808496"] = Vector3.new(2009.55859375, -26.37396240234375, -40.442588806152344),

          ["5116798298"] = Vector3.new(1806.794189453125, 11.464569091796875, -73.22099304199219),

          ["2896054889"] = Vector3.new(640.41552734375, 111.19093322753906, 64.45394897460938),

        }

      

        local character = player.Character

        local humanoid = character:FindFirstChildOfClass("Humanoid")

      

        local cframe = CFrame.new(character.PrimaryPart.Position, character.PrimaryPart.Position + humanoid.RootPart.CFrame.LookVector * 2)

      

        spawn(function()

          replicatedStorage.Remotes.ParryAttempt:FireServer(0, cframe, parryData, { 0, 0 })

        end)

      end

      

      local function ToggleSpamming()

        isSpamming = not isSpamming

        button.Text = isSpamming and "Stop Spamming" or "Start Spamming"

      

        if isSpamming then

          spammingConnection = runService.RenderStepped:Connect(function()

            if tick() - lastSpamTime >= spamInterval then

              Parry2()

              Parry()

              lastSpamTime = tick()

            end

          end)

        else

          if spammingConnection then

            spammingConnection:Disconnect()

          end

        end

      end

      

      local eKeyPressed = false

      

      userInputService.InputBegan:Connect(function(input, gameProcessedEvent)

          if not gameProcessedEvent and input.KeyCode == Enum.KeyCode.E then

              eKeyPressed = true

              ToggleSpamming()

          end

      end)

      

      userInputService.InputEnded:Connect(function(input)

          if input.KeyCode == Enum.KeyCode.E then

              eKeyPressed = false

          end

      end)

      

      button.MouseButton1Click:Connect(ToggleSpamming)

      

      runService.Heartbeat:Connect(function()

          labels.Ping.Text = "Ping: " .. networkStats["Data Ping"]:GetValueString()

          labels.FPS.Text = "FPS " .. string.split(workspaceStats.Heartbeat:GetValueString(), ".")[1]

      end)  

    end

  })

  

  local ToggleFPS = Tabs.Misc:AddToggle("ToggleAutoParry", {

    Title = "FPS Unlocker",

    Default = false,

    Callback = function(value)

      getgenv().boostFPS = value

  

      repeat task.wait() until game:IsLoaded();

      

      local vim = game:GetService("VirtualInputManager")

      local CoreGui = game:GetService("CoreGui")

      

      

      setfpscap(5000)

      

      CoreGui.DescendantAdded:Connect(function(d)

         if d.Name == "MainView" and d.Parent.Name == "DevConsoleUI" and boostFPS then

             task.wait()

             local screen = d.Parent.Parent.Parent

             screen.Enabled = false;

         end

      end)

      

      vim:SendKeyEvent(true, "F9", 0, game)    

      wait()

      vim:SendKeyEvent(false, "F9", 0, game)  

      

      setfpscap(5000)

      

      task.spawn(function()

        while true do task.wait()

      

              if not getgenv().boostFPS then

                  local panel = CoreGui:FindFirstChild("DevConsoleMaster", true);

      

                  if panel then

                      panel.Enabled = true;

                      vim:SendKeyEvent(true, "F9", 0, game)    

                      vim:SendKeyEvent(false, "F9", 0, game)  

                      repeat task.wait() until boostFPS

                  end

      

                  continue;

              end

      

      

          warn("")

      

          if not CoreGui:FindFirstChild("DevConsoleUI", true):FindFirstChild("MainView") then

            vim:SendKeyEvent(true, "F9", 0, game)    

            wait()

            vim:SendKeyEvent(false, "F9", 0, game)  

            continue

          end

        end

      end)

    end

  })

  

  -- // main shit \\ --

  

  local function Parry(OBJ)

    local function getplayerids()

        local ids = {}

        for i, player in pairs(game.Players:GetPlayers()) do

            ids[player.UserId] = player.Character.Head

        end

        return ids

    end

    if not bug_ball_method_____________________________________init then

        Remotes.Parry:Fire()

    else

        game.ReplicatedStorage.Remotes.ParryAttempt:FireServer(0.5, Player.Character.HumanoidRootPart.CFrame, getplayerids(), {100, 100})

    end

    ParryCD = true

    OBJ:SetAttribute('target', '')

    task.delay(.1, function()

        ParryCD = false

    end)

end

 

-- // calculations \\ --

 

local function calculatePredictionTime(ball, player, serverPing)

    if ball and ball.Position and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then

        local plr = game.Players.LocalPlayer

        local dist = (plr.Character.HumanoidRootPart.Position - ball.Position).Magnitude

        local vel = ball.Velocity.Magnitude

 

        if vel > 0 then

            local timeToCollision = dist / vel

            timeToCollision = timeToCollision + serverPing

 

            return timeToCollision

        end

    end

 

    return nil

end

 

-- // auto parry \\ --

 

RunService.Stepped:Connect(function(Time, DeltaTime)

    for i, ball in pairs(workspace.Balls:GetChildren()) do

        if ball:GetAttribute('realBall') then

            local ballVelocity = ball.Velocity

            local ballMagnitude = ballVelocity.Magnitude / 3

            local timeToImpact = calculatePredictionTime(ball, Player, game.Stats.Network.ServerStatsItem["Data Ping"]:GetValue() / 1000)

            local ballVolume = Vector3.new(ballVelocity.X * timeToImpact, ballVelocity.Y * timeToImpact, ballVelocity.Z * timeToImpact)

 

            if Visual then

                HitboxPart.Position = Player.Character.HumanoidRootPart.Position

                if ballVolume >= 1 then

                    HitboxPart.Size = Vector3.new(ballVolume, ballVolume, ballVolume)

                elseif ballVolume <= 5 then

                    HitboxPart.Size = Vector3.new(ballVolume, ballVolume, ballVolume)

                else

                    HitboxPart.Size = -Vector3.new(ballVolume, ballVolume, ballVolume)

                end

            else

                HitboxPart.Position = Vector3.new(0, 100000, 0)

            end

 

            if ball:GetAttribute('target') == (Player.Name or Player.DisplayName) and not ParryCD then

                if Parry then

                    if timeToImpact and (ballVolume <= ballMagnitude) then

                        Parry(ball)

                    end

                end

            end

        end

    end

 

    radius.Color = if ball:GetAttribute('target') == (Player.Name or Player.DisplayName) then

        Color3.fromHex('#FF0000')

    else

        Color3.fromHex('#1EFF00')

    end

end)

  

  -- // settings \\ --

  

  SaveManager:SetLibrary(Fluent)

  InterfaceManager:SetLibrary(Fluent)

  

  SaveManager:IgnoreThemeSettings()

  

  SaveManager:SetIgnoreIndexes({})

  

  InterfaceManager:SetFolder("VariousScriptHub")

  SaveManager:SetFolder("VariousScriptHub/Blade-Ball")

  

  InterfaceManager:BuildInterfaceSection(Tabs.Settings)

  SaveManager:BuildConfigSection(Tabs.Settings)

  

  Window:SelectTab(Tabs.Combat)

  

  Fluent:Notify({

      Title = "Vision",

      Content = "Your Gaming Chair🔥 (Forex sucks my dick)",

      Duration = 8

  })

  

  SaveManager:LoadAutoloadConfig()

 

-- Suck my dick forex, ezzz

