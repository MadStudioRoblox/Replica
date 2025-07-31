Replica is a Roblox server to client state replication solution which lets the developer subscribe certain players to certain states.

Individual states in the Replica module are called "replicas". Replicas can only be created and changed server-side, both server
and client can connect cleanup tasks for the moment of replica destruction, state changes can trigger listeners on the client-side.

## Basic example

### Server-side

``` luau linenums="1"
local Replica = require(game.ServerScriptService.ReplicaServer)

local Token = Replica.Token("GlobalData")

local replica = Replica.New({
    Token = Token,
    Data = { -- Passed table reference will be used
        Score = 0,
        Nested = {
            Value = false,
        },
    },
})

replica:Replicate()

task.spawn(function()
    while true do
        replica:Set({"Score"}, replica.Data.Score + 100)
        task.wait(1)
    end
end)

replica:Set({"Nested", "Value"}, true)
```

### Client-side

``` luau linenums="1"
local Replica = require(game.ReplicatedStorage.ReplicaClient)

Replica.OnNew("GlobalData", function(replica)

    print(`Replica received client-side! Data:`, replica.Data)

    replica:OnSet({"Score"}, function(new_value, old_value)
        print(`Score has changed from {old_value} to {new_value}`)
    end)

end)

Replica.RequestData() -- Must be called once anywhere in game code client-side
```