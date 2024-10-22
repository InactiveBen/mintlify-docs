### Example Code
Want to try out Fluent without having your own custom interface? We offer an example script just for that reason!





<Warning>
You must have the Fluent Library installed in your workspace for this to work.
<Accordion title="How do I get the Fluent Library?" icon="rocket"> 
  <Card
    title="Fluent Library Setup"
    icon="book"
    href="https://docs.fishbowl.cloud/quickstart"
  >
    <p>Step-by-step instructions to install and configure the Fluent Library. Get started quickly with our detailed setup guide!</p>
  </Card>
</Accordion>
</Warning>
Create a `LocalScript` under `StarterPlayerScripts` to use the examples below. Keep in mind that the Fluent Library must be installed prior to using these examples.

<CodeGroup>
```lua Example 1: Fluent Preview
task.wait(3)
local Main = require(game:GetService("ReplicatedStorage").Fluent:WaitForChild("MainModule"))

local Window = Main:CreateWindow({
    Title = "Fluent " .. Main.Version,
    SubTitle = "by zuq",
    TabWidth = 160,
    Size = UDim2.fromOffset(580, 460),
    Acrylic = true,
    Theme = "Dark"
})

local Tabs = {
    Main = Window:AddTab({ Title = "Main", Icon = "box" }),
    Settings = Window:AddTab({ Title = "Settings", Icon = "settings" })
}

do
    Tabs.Main:AddParagraph({
        Title = "Paragraph",
        Content = "This is a paragraph.\nSecond line!"
    })

    Tabs.Main:AddButton({
        Title = "Button",
        Description = "Very important button",
        Callback = function()
            Window:Dialog({
                Title = "Title",
                Content = "This is a dialog",
                Buttons = {
                    {
                        Title = "Confirm",
                        Callback = function()
                            Window:Dialog({
                                Title = "Another Dialog",
                                Content = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Suspendisse mollis dolor eget erat mattis, id mollis mauris cursus. Proin ornare sollicitudin odio, id posuere diam luctus id.",
                                Buttons = { { Title = "Ok", Callback = function() print("Ok") end} }
                            })
                            Main.Options.Toggle:Destroy()
                        end
                    },
                    {
                        Title = "Cancel",
                        Callback = function()
                            print("Cancelled the dialog.")
                        end
                    }
                }
            })
        end
    })

    local Toggle = Tabs.Main:AddToggle("Toggle", {Title = "Toggle", Default = false })
    
    local Slider = Tabs.Main:AddSlider("Slider", {
        Title = "Slider",
        Description = "This is a slider",
        Default = 2.0,
        Min = 0.0,
        Max = 15.5,
        Rounding = 1
    })

    local Dropdown = Tabs.Main:AddDropdown("Dropdown", {
        Title = "Dropdown",
        Values = {"one", "two", "three", "four", "five", "six", "seven", "eight", "nine", "ten", "eleven", "twelve", "thirteen", "fourteen"},
        Multi = false,
        Default = 1,
    })

    Dropdown:SetValue("four")
    
    local MultiDropdown = Tabs.Main:AddDropdown("MultiDropdown", {
        Title = "Dropdown",
        Description = "You can select multiple values.",
        Values = {"one", "two", "three", "four", "five", "six", "seven", "eight", "nine", "ten", "eleven", "twelve", "thirteen", "fourteen"},
        Multi = true,
        Default = {"seven", "twelve"},
    })

    MultiDropdown:SetValue({
        three = true,
        five = true,
        seven = false
    })

    local Colorpicker = Tabs.Main:AddColorpicker("Colorpicker", {
        Title = "Colorpicker",
        Default = Color3.fromRGB(96, 205, 255)
    })

    local TColorpicker = Tabs.Main:AddColorpicker("TransparencyColorpicker", {
        Title = "Colorpicker",
        Description = "but you can change the transparency.",
        Transparency = 0,
        Default = Color3.fromRGB(96, 205, 255)
    })

    local Keybind = Tabs.Main:AddKeybind("Keybind", {
        Title = "KeyBind",
        Mode = "Hold",
        Default = "LeftControl",
        ChangedCallback = function(New)
            print("Keybind changed:", New)
        end
    })

    local Input = Tabs.Main:AddInput("Input", {
        Title = "Input",
        Default = "Default",
        Numeric = false,
        Finished = false,
        Placeholder = "Placeholder text",
        Callback = function(Value)
            print("Input changed:", Value)
        end
    })
       
    Toggle:OnChanged(function()
        print("Toggle changed:", Main.Options["Toggle"].Value)
    end)

    Slider:OnChanged(function(Value)
        print("Slider changed:", Value)
    end)

    Dropdown:OnChanged(function(Value)
        print("Dropdown changed:", Value)
    end)

    MultiDropdown:OnChanged(function(Value)
        local Values = {}
        for Value, State in next, Value do
            table.insert(Values, Value)
        end
        print("Mutlidropdown changed:", table.concat(Values, ", "))
    end)

    Colorpicker:OnChanged(function()
        print("Colorpicker changed:", TColorpicker.Value)
    end)

    TColorpicker:OnChanged(function()
        print(
            "TColorpicker changed:", TColorpicker.Value,
            "Transparency:", TColorpicker.Transparency
        )
    end)

    task.spawn(function()
        while true do
            wait(1)
            local state = Keybind:GetState()
            if state then
                print("Keybind is being held down")
            end
            if Main.Unloaded then break end
        end
    end)

end

do
    local InterfaceSection = Tabs.Settings:AddSection("Interface")

    InterfaceSection:AddDropdown("InterfaceTheme", {
        Title = "Theme",
        Description = "Changes the interface theme.",
        Values = Main.Themes,
        Default = Main.Theme,
        Callback = function(Value)
            Main:SetTheme(Value)
        end
    })

    if Main.UseAcrylic then
        InterfaceSection:AddToggle("AcrylicToggle", {
            Title = "Acrylic",
            Description = "The blurred background requires graphic quality 8+",
            Default = Main.Acrylic,
            Callback = function(Value)
                Main:ToggleAcrylic(Value)
            end
        })
    end

    InterfaceSection:AddToggle("TransparentToggle", {
        Title = "Transparency",
        Description = "Makes the interface transparent.",
        Default = Main.Transparency,
        Callback = function(Value)
            Main:ToggleTransparency(Value)
        end
    })

    InterfaceSection:AddKeybind("MenuKeybind", { Title = "Minimize Bind", Default = "RightShift" })
    Main.MinimizeKeybind = Main.Options.MenuKeybind 
end

Main:Notify({
    Title = "Fluent",
    Content = "The script has been loaded.",
    Duration = 8
})
```
```lua Example 2: Basic Notification and Input Handling
task.wait(3)
local Main = require(game:GetService("ReplicatedStorage").Fluent:WaitForChild("MainModule"))

local Window = Main:CreateWindow({
    Title = "Notification Example",
    SubTitle = "by zuq",
    Size = UDim2.fromOffset(400, 300),
    Acrylic = false,
    Theme = "Light"
})

local Tabs = {
    Notifications = Window:AddTab({ Title = "Notifications", Icon = "bell" }),
}

Tabs.Notifications:AddButton({
    Title = "Show Notification",
    Description = "Click to show a notification.",
    Callback = function()
        Main:Notify({
            Title = "Notification",
            Content = "This is a notification message!",
            Duration = 5
        })
    end
})

Tabs.Notifications:AddInput("NameInput", {
    Title = "Enter Your Name",
    Default = "",
    Placeholder = "Type your name here...",
    Callback = function(value)
        Main:Notify({
            Title = "Input Received",
            Content = "Hello, " .. value .. "!",
            Duration = 5
        })
    end
})

Main:Notify({
    Title = "Ready",
    Content = "You can now test the notifications.",
    Duration = 5
})
```
```lua Example 3: Slider and Colorpicker Interaction
task.wait(1)
local Main = require(game:GetService("ReplicatedStorage").Fluent:WaitForChild("MainModule"))

local Window = Main:CreateWindow({
    Title = "Slider & Color Picker Example",
    SubTitle = "by zuq",
    Size = UDim2.fromOffset(500, 400),
    Acrylic = true,
    Theme = "Dark"
})

local Tabs = {
    Sliders = Window:AddTab({ Title = "Sliders", Icon = "slider" }),
}

local Slider = Tabs.Sliders:AddSlider("ColorSlider", {
    Title = "Adjust Color Value",
    Description = "Change the RGB value.",
    Default = 128,
    Min = 0,
    Max = 255,
    Rounding = 0
})

local ColorPicker = Tabs.Sliders:AddColorpicker("ColorPicker", {
    Title = "Choose a Color",
    Default = Color3.fromRGB(255, 255, 255)
})

Slider:OnChanged(function(value)
    local color = Color3.fromRGB(value, 0, 255)
    ColorPicker:SetValue(color)
end)

ColorPicker:OnChanged(function(color)
    print("Color chosen:", color)
end)

Main:Notify({
    Title = "Ready to Use",
    Content = "You can adjust the slider and see the color change.",
    Duration = 5
})
```
```lua Example 4: Keybind and Toggle Interaction
task.wait(1)
local Main = require(game:GetService("ReplicatedStorage").Fluent:WaitForChild("MainModule"))

local Window = Main:CreateWindow({
    Title = "Keybind & Toggle Example",
    SubTitle = "by zuq",
    Size = UDim2.fromOffset(450, 350),
    Acrylic = false,
    Theme = "Light"
})

local Tabs = {
    Controls = Window:AddTab({ Title = "Controls", Icon = "controls" }),
}

local Keybind = Tabs.Controls:AddKeybind("ToggleKeybind", {
    Title = "Toggle Action",
    Mode = "Hold",
    Default = "M",
    ChangedCallback = function(new)
        print("Keybind changed to:", new)
    end
})

local Toggle = Tabs.Controls:AddToggle("ActionToggle", {
    Title = "Enable Action",
    Default = false,
    Callback = function(value)
        print("Toggle is now:", value and "Enabled" or "Disabled")
    end
})

task.spawn(function()
    while true do
        wait(0.5)
        if Keybind:GetState() then
            print("Keybind is currently held down")
            Toggle:SetValue(not Toggle.Value) -- Toggle the state when the key is pressed
        end
    end
end)

Main:Notify({
    Title = "Controls Ready",
    Content = "Use 'M' to toggle the action.",
    Duration = 5
})

```
```lua Example 5: Multi-Tab Interface with Settings
task.wait(1)
local Main = require(game:GetService("ReplicatedStorage").Fluent:WaitForChild("MainModule"))

local Window = Main:CreateWindow({
    Title = "Multi-Tab Example",
    SubTitle = "by zuq",
    Size = UDim2.fromOffset(600, 500),
    Acrylic = true,
    Theme = "Dark"
})

local Tabs = {
    Main = Window:AddTab({ Title = "Home", Icon = "home" }),
    Settings = Window:AddTab({ Title = "Settings", Icon = "settings" }),
}

Tabs.Main:AddParagraph({
    Title = "Welcome",
    Content = "This is the main tab. Use the settings tab to customize."
})

Tabs.Settings:AddToggle("EnableFeature", {
    Title = "Enable New Feature",
    Default = false,
    Callback = function(value)
        print("New Feature Enabled:", value)
    end
})

Tabs.Settings:AddDropdown("SelectOption", {
    Title = "Choose an Option",
    Values = {"Option 1", "Option 2", "Option 3"},
    Default = 1,
    Callback = function(value)
        print("Selected Option:", value)
    end
})

Main:Notify({
    Title = "Welcome",
    Content = "Explore the tabs for more options!",
    Duration = 5
})


```
</CodeGroup>