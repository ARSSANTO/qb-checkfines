# qb-checkfines
qb-checkfines is a small snippet for check unpaid fines from citizen I was looking for bill checker for my server so i am sharing it with qb-core family hope it will be help full for you 

##Installation

- Check Invoice Client Register Can be slided on any client side preffer police job or wherever you want 
```bash
RegisterNetEvent('qb-billing:client:checkFines', function()
    local player, distance = QBCore.Functions.GetClosestPlayer()
    if player ~= -1 and distance < 2.5 then
        local PlayerId = GetPlayerServerId(player)
        QBCore.Functions.TriggerCallback('qb-billing:server:checkFines', function(invoices, Cid)
            local InvoiceShow = {
                {
                    header = 'Unpaid Invoices | ID: ' .. PlayerId,
                    isMenuHeader = true,
                    icon = 'fas fa-file-invoice-dollar',
                },
                {
                    header = 'Citizen ID: ' .. Cid,
                    isMenuHeader = true,
                    icon = 'fas fa-id-card-clip',
                },
            }
            for _, v in ipairs(invoices) do
                InvoiceShow[#InvoiceShow + 1] = {
                    header = 'Amount: ' .. v.amount,
                    icon = 'fas fa-dollar-sign',
                    txt = 'Sender: ' .. v.sender .. ' | Society: ' .. v.society,
                    params = { event = 'qb-billing:open:invoiceMainmenu', } --You can trigger to go back in main menu
                }
            end
            InvoiceShow[#InvoiceShow + 1] = {
                header = 'Close',
                icon   = 'fa-solid fa-circle-xmark',
                txt    = '',
                params = { event = 'qb-menu:closeMenu', }
            }
            exports['qb-menu']:openMenu(InvoiceShow)
        end, PlayerId)
    else
        QBCore.Functions.Notify('No one around!', 'error')
    end
end)

```
- Server Side Callback  you can put this on server side of that script where you puted the client side
```bash
QBCore.Functions.CreateCallback('qb-billing:server:checkFines', function(source, cb, target)
	local Player = QBCore.Functions.GetPlayer(target)
	if Player then
		local Cid = Player.PlayerData.citizenid
		MySQL.Async.fetchAll('SELECT amount, id, society, sender FROM phone_invoices WHERE citizenid = ?', {Cid}, function(invoices)
			cb(invoices, Cid)
		end)
	else
		cb({})
	end
end)
```
- Command to check Invoices as police ems and mechanic can be slide after the call back
```bash
QBCore.Commands.Add('checkfine', 'Check Invoice', {help = 'checkfine'}, true, function(source)
    local Myself = QBCore.Functions.GetPlayer(source)
    if Myself.PlayerData.job.name == 'police' or Myself.PlayerData.job.name == 'ambulance' or or Myself.PlayerData.job.name == 'mechanic' then
        TriggerClientEvent('qb-billing:client:checkFines')
    else
        TriggerClientEvent('QBCore:Notify', source, 'You are not a Service Worker')
    end
end)
```
- Also you can triger this client event from wherever you want  ``qb-billing:client:checkFines`` Trigger this fromradial menu 
##Future Plans & Contributions
If You wanna contribute feel free to do it.. i have plans to give propper setup of billing menu with a help of qb-menu and input but there are plenty on github no no plans for now lemmi know by creating suggestion or issues 

##Dependency
- qb-phone https://github.com/qbcore-framework/qb-phone
- qb-menu https://github.com/qbcore-framework/qb-menu

##ShowCase


https://cdn.discordapp.com/attachments/889236522698694697/974620902010540042/unknown.png
