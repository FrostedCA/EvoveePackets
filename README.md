# EvoveePackets
Small packet library made for Evovee. You can use and modify it as you wish. It uses Json so you will need to have json dependencies (Jackson Core).
For more details contact me on discord: **landryman**.
Version 2.00+ stable.

## Download
[![](https://www.jitpack.io/v/FrostedCA/EvoveePackets.svg)](https://www.jitpack.io/#FrostedCA/EvoveePackets)

## Example client-side child packet class:
#### With Update v2.11
```java
public class CPacketRegister extends Packet {

    public CPacketRegister(ISession session) {
        super(session);
    }

    public CPacketRegister(ISession session, RegisterObject registerObject) {
        super(session);
        this.pObject = registerObject;
    }

    @Override
    public void read(JsonNode jsonNode, ObjectMapper objectMapper) throws JsonProcessingException {
        if(!jsonNode.isNull() && !jsonNode.isEmpty()) {
            RegisterObject registerObject = objectMapper.treeToValue(jsonNode, RegisterObject.class);
            App.getSession().getPacketsManager().writePacket(new CPacketLogin(App.getSession(), registerObject));
        }
    }
}
```

Example server-side child packet class:
#### With Update v2.11
```java
public class SPacketRegister extends Packet {

    public SPacketRegister(ISession session) {
        super(session);
    }

    public SPacketRegister(ISession session, RegisterObject registerObject) {
        super(session);
        this.pObject = registerObject;
    }

    @Override
    public void read(JsonNode jsonNode, ObjectMapper objectMapper) throws JsonProcessingException {
        boolean result = false;

        RegisterObject registerObject = objectMapper.treeToValue(jsonNode, RegisterObject.class);

        boolean valid = InputValidator.validatePassword(registerObject.getPassword(), registerObject.getConfPassword());
        if(valid) {
            result = DBQueries.registerAccount(registerObject);
        }
        if(result) {
            ((ClientSession) session).getPacketsManager().writePacket(new SPacketRegister(session, registerObject));
        }
    }
}
```
