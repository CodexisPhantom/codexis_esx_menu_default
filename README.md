<h1 align='center'>[ESX] Modified Menu Default</a></h1><p align='center'><b><a href='https://discord.esx-framework.org/'>Discord</a> - <a href='https://esx-framework.org/'>Website</a> - <a href='https://docs.esx-framework.org/legacy/installation'>Documentation</a></b></h5>

A modified default List type menu for ESX that add a banner system (using links) and alignement is now fix you cannot change it.

# Usage

Add in the data an image

```lua
ESX.UI.Menu.Open('default', GetCurrentResourceName(), 'test', {
	title = 'Test',
 	image = 'links to a banner',
  	elements = {},
}, function(data, menu)
	-- Your code
 end, function(data, menu)
 	-- Your code
end)
```

# Installation

Don't forget to replace this function in es_extended/client/functions.lua.

```lua
function ESX.UI.Menu.Open(type, namespace, name, data, submit, cancel, change, close)
    local menu = {}

    menu.type = type
    menu.namespace = namespace
    menu.name = name
    menu.data = data
    menu.submit = submit
    menu.cancel = cancel
    menu.change = change

    if not data.image or (not type(data.image) == 'string') then
        data.image = 'your link to a default banner'
    end

    menu.close = function()

        ESX.UI.Menu.RegisteredTypes[type].close(namespace, name)

        for i = 1, #ESX.UI.Menu.Opened, 1 do
            if ESX.UI.Menu.Opened[i] then
                if ESX.UI.Menu.Opened[i].type == type and ESX.UI.Menu.Opened[i].namespace == namespace and
                    ESX.UI.Menu.Opened[i].name == name then
                    ESX.UI.Menu.Opened[i] = nil
                end
            end
        end

        if close then
            close()
        end

    end

    menu.update = function(query, newData)

        for i = 1, #menu.data.elements, 1 do
            local match = true

            for k, v in pairs(query) do
                if menu.data.elements[i][k] ~= v then
                    match = false
                end
            end

            if match then
                for k, v in pairs(newData) do
                    menu.data.elements[i][k] = v
                end
            end
        end

    end

    menu.refresh = function()
        ESX.UI.Menu.RegisteredTypes[type].open(namespace, name, menu.data)
    end

    menu.setElement = function(i, key, val)
        menu.data.elements[i][key] = val
    end

    menu.setElements = function(newElements)
        menu.data.elements = newElements
    end

    menu.setTitle = function(val)
        menu.data.title = val
    end

    menu.removeElement = function(query)
        for i = 1, #menu.data.elements, 1 do
            for k, v in pairs(query) do
                if menu.data.elements[i] then
                    if menu.data.elements[i][k] == v then
                        table.remove(menu.data.elements, i)
                        break
                    end
                end

            end
        end
    end

    ESX.UI.Menu.Opened[#ESX.UI.Menu.Opened + 1] = menu
    ESX.UI.Menu.RegisteredTypes[type].open(namespace, name, data)

    return menu
end
```
