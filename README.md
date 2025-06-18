-- Configurações iniciais
local ESP_ENABLED = true
local DRAW_DISTANCE = 100  -- Distância máxima para mostrar o ESP

-- Cores para os jogadores
local enemyColor = { r = 255, g = 0, b = 0 }
local teamColor = { r = 0, g = 255, b = 0 }

-- Simulação de função que retorna todos os jogadores
-- Substitua por sua função específica do framework (como GetPlayers())
function GetAllPlayers()
    return GetPlayers()  -- Exemplo genérico
end

-- Simulação de função para desenhar texto na tela
function DrawText3D(x, y, z, text, color)
    SetTextFont(0)
    SetTextProportional(1)
    SetTextScale(0.35, 0.35)
    SetTextColour(color.r, color.g, color.b, 255)
    SetTextEntry("STRING")
    AddTextComponentString(text)
    SetDrawOrigin(x, y, z, 0)
    DrawText(0.0, 0.0)
    ClearDrawOrigin()
end

-- Checagem de time (você pode ajustar isso com base no seu jogo)
function IsSameTeam(player1, player2)
    local team1 = GetPlayerTeam(player1)
    local team2 = GetPlayerTeam(player2)
    return team1 == team2
end

-- Loop principal do ESP
Citizen.CreateThread(function()
    while true do
        Wait(1)
        if ESP_ENABLED then
            local localPlayer = PlayerId()
            local localPed = PlayerPedId()
            local localCoords = GetEntityCoords(localPed)

            for _, player in ipairs(GetAllPlayers()) do
                if player ~= localPlayer and NetworkIsPlayerActive(player) then
                    local ped = GetPlayerPed(player)
                    if DoesEntityExist(ped) and not IsEntityDead(ped) then
                        local coords = GetEntityCoords(ped)
                        local distance = #(localCoords - coords)
                        if distance <= DRAW_DISTANCE then
                            local sameTeam = IsSameTeam(localPlayer, player)
                            if not sameTeam then
                                DrawText3D(coords.x, coords.y, coords.z + 1.0, "Inimigo - " .. math.floor(distance) .. "m", enemyColor)
                            end
                        end
                    end
                end
            end
        end
    end
end)

-- Interface simples: comando para ativar/desativar ESP
RegisterCommand("toggleesp", function()
    ESP_ENABLED = not ESP_ENABLED
    print("ESP: " .. (ESP_ENABLED and "Ativado" or "carregando"))
end)
