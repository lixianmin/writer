1. redis.call()可以返回各种类型的数据，比如integer, string等
2. 但是hget key val返回的，只能是字符串，即使可以使用hincrby设值，那也是字符串



```lua

local prefix = 'bot_tourwords:'
local maxRoomUser = tonumber(KEYS[1])
local roomUsersNum = prefix .. KEYS[2]
local roomName = prefix .. KEYS[3]

-- use then old room
local lastNum = tonumber(redis.call('hget', roomName, 'num'))
if lastNum and lastNum < maxRoomUser then
  local roomId = redis.call('hget', roomName, 'room_id')  
  redis.call('hincrby', roomName, 'num', 1)
  redis.call('zincrby', roomUsersNum, 1, roomId)
  return roomId
else
  -- open an new room
  local roomIds = redis.call('zRevRangeByScore', roomUsersNum, 0, 0, 'limit', 0, 1)
  if #roomIds == 1 then
    local roomId = roomIds[1]
    redis.call('hset', roomName, 'room_id', roomId)
    redis.call('hset', roomName, 'num', 1)
    redis.call('zadd', roomUsersNum, 1, roomId)
    return roomId
  end
  
  return ''
end

```



1. script load "xxxx"
2. evalsha 808b7a44b6f9b3af2a783af54ade493c1e4c69b5 3 4 debug_room_users_num camera