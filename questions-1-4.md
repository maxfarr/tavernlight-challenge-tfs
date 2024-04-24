# Q1 - Fix or improve the implementation of the below methods

```
local function releaseStorage(player)
player:setStorageValue(1000, -1)
end

function onLogout(player)
if player:getStorageValue(1000) == 1 then
addEvent(releaseStorage, 1000, player)
end
return true
end
```

## Comments:

This function is problematic because it attempts to setStorageValue on a player who is already logged out due to the 1-second wait. We could use a database query to update the value after the player is logged out, but without any further context, I’m assuming the intention is to clear the value right away. In that case, we can clear the storage right away before the logout completes.
We can also check for any value which is not -1, in case we want to store values besides 1 later on.
Finally, we should factor out the magic number.

## Fixed:

```
PLAYER_STORAGE_ID = 1000

local function releaseStorage(player)
    player:setStorageValue(PLAYER_STORAGE_ID, -1)
end

function onLogout(player)
    if player:getStorageValue(PLAYER_STORAGE_ID) != -1 then
        releaseStorage(player)
    end
    return true
end
```

# Q2 - Fix or improve the implementation of the below method

```
function printSmallGuildNames(memberCount)
-- this method is supposed to print names of all guilds that have less than memberCount max members
local selectGuildQuery = "SELECT name FROM guilds WHERE max_members < %d;"
local resultId = db.storeQuery(string.format(selectGuildQuery, memberCount))
local guildName = result.getString("name")
print(guildName)
end
```

## Comments:

There are a few problems here:

- resultId is not passed to the getString call.
- resultId is not checked for validity before usage.
- The code does not iterate through all the rows of the query - it prints the first retrieved name, and then exits.

## Fixed:

```
function printSmallGuildNames(memberCount)
    -- this method is supposed to print names of all guilds that have less than memberCount max members
    local selectGuildQuery = "SELECT name FROM guilds WHERE max_members < %d;"
    local resultId = db.storeQuery(string.format(selectGuildQuery, memberCount))

    if resultId then
        repeat
            local guildName = result.getString(resultId, "name")
            print(guildName)
        until not result.next(resultId)
    end
end
```

# Q3 - Fix or improve the name and the implementation of the below method

```
function do_sth_with_PlayerParty(playerId, membername)
player = Player(playerId)
local party = player:getParty()wdsd

for k,v in pairs(party:getMembers()) do
if v == Player(membername) then
party:removeMember(Player(membername))
end
end
end
```

## Comments:

First of all, this function needs a helpful name! In this case, it looks like we’re trying to remove a member of the given player’s party, so we can call it kickPartyMember.
There are also a few problems with the implementation:

- We need to check for nil Player, to prevent errors and instead exit the function gracefully.
- Similarly, we should check for a nil Party, in the case where the player is not currently in a party.
- For this example, I also assume that if the player is not the leader of their party, the kick should not be handled.
- We might want to add some debug prints to indicate when the above cases are handled, plus the case in which a matching party member is not found. To support this functionality, we should return as soon as we find a match and kick the relevant player. (This will also prevent us from making unnecessary checks after finding a match.)
- Other nitpicks: capitalization of parameter name, variable “k” in loop is unnecessary and can be anonymous, variable “v” could have a more descriptive name, no need to reconstruct Player since we can just check for the name

## Fixed:

```
function kickPartyMember(playerId, memberName)
    player = Player(playerId)
    if not player then
        print(string.format(“Player id=%d not found!”, playerId))
        return
    end

    local party = player:getParty()
    if not party then
        print(string.format(“Player id=%d is not currently in a party.”, playerId))
        return
    end

    if party:getLeader() != player then
        print(“Player id=%d is not the leader of their party.”, playerId)
        return
    end

    for _,member in pairs(party:getMembers()) do
        if member:getName() == memberName then
            party:removeMember(member)
            return;
        end
    end

    print(string.format(“Player with name %s was not found in the party.”, memberName))
end
```

# Q4 - Assume all method calls work fine. Fix the memory leak issue in below method

```
void Game::addItemToPlayer(const std::string& recipient, uint16_t itemId)
{
Player\* player = g_game.getPlayerByName(recipient);
if (!player) {
player = new Player(nullptr);
if (!IOLoginData::loadPlayerByName(player, recipient)) {
return;
}
}

Item\* item = Item::CreateItem(itemId);
if (!item) {
return;
}

g_game.internalAddItem(player->getInbox(), item, INDEX_WHEREEVER, FLAG_NOLIMIT);

if (player->isOffline()) {
IOLoginData::savePlayer(player);
}
}
```

## Comments:

In the case where we can’t find the login data for the player, we need to delete the Player we just allocated. We don’t need to do the same for item, however, because if we get a nullptr, there’s nothing to delete.

## Fixed:

```
void Game::addItemToPlayer(const std::string& recipient, uint16_t itemId)
{
    Player\* player = g_game.getPlayerByName(recipient);
    if (!player) {
        player = new Player(nullptr);
        if (!IOLoginData::loadPlayerByName(player, recipient)) {
            delete player;
            return;
        }
    }

    Item* item = Item::CreateItem(itemId);
    if (!item) {
        return;
    }

    g_game.internalAddItem(player->getInbox(), item, INDEX_WHEREEVER, FLAG_NOLIMIT);

    if (player->isOffline()) {
        IOLoginData::savePlayer(player);
    }

}
```
