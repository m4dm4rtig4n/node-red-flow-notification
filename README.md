# node-red-flow-notification

Liens vers le flow : [ici](export.flow)

Petit screen :

![Screen](2020-12-16_10-48.png)

**Information**

Ce subflow va vous permettre de notifier plusieurs services en même temps.

Actuellement il gére les services :

- HTML5 (by Home Assistant)
- Discord
- Mattermost
- Telegram
- Email
- TTS
- Home Assistant (Mobile App)
- SMS via HTTP
- Awtrix
- Appel Telegram (avec CallMeBot)
- Signal (avec CallMeBot)
- WhatsApp (avec CallMeBot)
- Facebook Messenger (avec CallMeBot)

**Pré-requis**

- node-red-contrib-string
- node-red-contrib-home-assistant-websocket
- node-red-node-email
- node-red-contrib-telegrambot

**Entrée**

***Node-RED :***

Voici la liste des inputs actuellement possible :

```
msg.title = "Home Assistant - New version available"
msg.dest = "tel_clement;mail_clement;tts_80_hall"  
msg.message = "Home Assistant : New version available => 0.113.0 \nhttps://github.com/home-assistant/core/releases/tag/0.113.0"
msg.short_message = "Home Assistant : New version available (0.113.0)"
msg.image = "/data/image.jpeg"
msg.document = "/data/doc.js"
msg.video = "/data/video.mp4"
```

***HTTP (GET) :***

Il est possible d'utiliser le service de notification via n'importe qu'elle autres services même ceux non reliée à Node-RED
via le endpoint HTTP (GET) `/notif`.

Exemple :
```
https://URL.VERS.NODERED/notif?title=test&dest=discord_maison_general&message=coucou
```

***Liste des inputs possible :***

Tout les inputs ne sont pas forcément compatible avec les différents services.

**Titre** / *msg.title*

*Disponible sur : Email*

Titre du mail.

*Exemple:*
```
msg.title = "Home Assistant - New version available"
````


**Message** / *msg.message*

*Disponible sur : Email / Telegram / Discord / CallMeBot*

Version longue du message.

*Exemple:*
```
msg.message = "Home Assistant : New version available => 0.113.0 \n https://github.com/home-assistant/core/releases/tag/0.113.0"
```
**Message court** / *msg.short_message*

*Disponible sur : HTML5 / Home Assistant*

Message reduit pour les services type « popup ».

*Exemple:*
```
msg.short_message = "Home Assistant : New version available (0.113.0)"
```

ATTENTION : Les messages est trim et les \n sont remplacer par des espace

=> Si il n’est pas défini, je reprend les msg.message

**Répétition du message** / *msg.repeat*

*Disponible sur : Telegram Voice (CallMeBot)*

Répète le message audio X fois.

*Exemple:*
```
msg.repeat = 2
```

**Image** / *msg.image*

*Disponible sur : Telegram / CallMeBot Messenger*

*Exemple:*
```
msg.image = "/data/image.jpeg"
msg.image = "https://cdn.pixabay.com/photo/2015/04/23/22/00/tree-736885__340.jpg"
```

**Document** / *msg.document*

*Disponible sur : Telegram*

*Exemple:*
```
msg.document = "/data/doc.js"
```

**Video** / *msg.video*

*Disponible sur : Telegram*

*Exemple:*
```
msg.video = "/data/video.mp4"
```

**Destinatation** / *msg.dest*

Un string qui contient la liste des services que vous désirez notifier séparer par des « ; »

*Exemple:*
```
msg.dest = "tel_clement;mail_clement;tts_80_hall"
```
  
Le message sera envoyé à :
- tel_clement
- mail_clement
- tts_80_hall

**HTML5**
La totalité des messages passe par ce système par défaut, pas besoin de l’ajouter en destination.

**Discord**

Dest doit commencer par discord_

*Exemple:*
```
msg.dest = "discord_communaute_news;discord_maison_information;discord_maison_network"
```  

**Home Assistant**

Dest doit commencer par tel_

*Exemple:*
```
msg.dest = "tel_clement;tel_papa;tel_maman
```

**Email**

dest doit commencer par mail_

*Exemple:*
```
msg.dest = "mail_clement;mail_thomas"
```

**Telegram**

dest doit commencer par telegram_

*Exemple:*
```
msg.dest = "telegram_smarthome;telegram_familly"
```

**TTS**

dest doit commencer par tts_

Format : tts_{VOLUME}_{ENTITY_ID_DU_MEDIA_PLAYER}

*Exemple:*
```
msg.dest = "tts_80_hall"
```

**SMS**
dest doit commencer par `sms_`

*Exemple:*
```
msg.dest = "sms_clement"
```

**Awtrix**
dest doit commencer par `awtrix_`

*Exemple:*
```
msg.dest = "awtrix_home"
```

**Telegram Voice (CallMeBot)**
dest doit commencer par `callmebot_telegram_voice_`

*Exemple:*
```
msg.dest = "callmebot_telegram_voice_clement"
```

**FacebookMessenger (CallMeBot)**
dest doit commencer par `callmebot_messenger_`

*Exemple:*
```
msg.dest = "callmebot_messenger_clement"
```

**Signal (CallMeBot)**
dest doit commencer par `callmebot_signal_`

*Exemple:*
```
msg.dest = "callmebot_signal_clement"
```

**Whatsapp (CallMeBot)**
dest doit commencer par `callmebot_whatsapp_`

*Exemple:*
```
msg.dest = "callmebot_whatsapp_clement"
```

**Configuration**

Le subflow récupére sa configuration via la variable Global notif.

TTS n’a besoin d’aucune configuration particulière

*Exemple:*

```
var configuration = 
{
    "discord": {
        "dest": {
            "discord_communaute_news": "https://discord.com/api/webhooks/XXXXXXXXXXXXXXXX",
            "discord_communaute_zwave": "https://discord.com/api/webhooks/YYYYYYYYYYYYYYYY",
            "discord_communaute_diy": "https://discord.com/api/webhooks/ZZZZZZZZZZZZZZZZZ",
        }
    },
    "home_assistant": {
        "dest": {
            "tel_clement": "mobile_app_oneplus_a6010",
            "tel_nathalie": "mobile_app_oneplus_a5000"
        }
    },
    "email": {
        "dest": {
            "mail_clement": "email1@domain.fr",
            "mail_john": "email2@domain.fr",
            "mail_bibi": "email3@domain.fr"        
        }
    },
    "telegram": {
        "dest": {
            "telegram_smarthome": "ID1",
            "telegram_bot": "ID2"        
        }
    },
    "mattermost": {
        "dest": {
            "mattermost_home" : "https://mattermost.domain.fr/hooks/XXXXXXXXXXXXXXXXXXXXXXXXX",
            "mattermost_volets" : "https://mattermost.domain.fr/hooks/YYYYYYYYYYYYYYYYYYYYYYYY",
        }
    },
    "sms": {
        "url": "http://192.168.1.1:8080/?action=sendSms&number=##SMSNUMBER##&message=##MESSAGE##",
        "dest": {
            "sms_clement": "06XXXXXXXXX",
            "sms_nathalie": "06YYYYYYYYY",
        }
    },
    "awtrix": {
        "dest": {
            "awtrix_home": "192.168.1.1:7000",
            "awtrix_desk": "192.168.1.2:7000",
        }
    },
    "callmebot": {
        "dest": {
            "callmebot_messenger_clement" : "https://api.callmebot.com/facebook/send.php?apikey=##APIKEY##",
            "callmebot_signal_clement" : "https://api.callmebot.com/signal/send.php?phone=##PHONENUMBER##&apikey=##APIKEY##",
            "callmebot_telegram_voice_clement" : "http://api.callmebot.com/start.php?user=##USERNAME##&lang=##LANGUAGE##",
            "callmebot_whatsapp_clement" : "https://api.callmebot.com/whatsapp.php?phone=##PHONENUMBER##&apikey=##APIKEY##"
        }
    }
}
global.set("notif",configuration)
```

Pour vous aidez dans la configuration voici une function qui va vous permettres de pousser votre configuration à chaque deploiement.

Tout ce passe dans le node Function.

```
[
    {
        "id": "bbda9cf1.ad8ba",
        "type": "inject",
        "z": "a9ae8a01.8a5bd8",
        "name": "",
        "props": [
            {
                "p": "payload"
            }
        ],
        "repeat": "",
        "crontab": "",
        "once": true,
        "onceDelay": 0.1,
        "topic": "",
        "payload": "",
        "payloadType": "date",
        "x": 290,
        "y": 260,
        "wires": [
            [
                "513d2c9.069c3d4"
            ]
        ]
    },
    {
        "id": "dacca689.055138",
        "type": "function",
        "z": "a9ae8a01.8a5bd8",
        "name": "Configurateur EXEMPLE",
        "func": "var configuration = \n{\n    \"discord\": {\n        \"dest\": {\n            \"discord_communaute_news\": \"https://discord.com/api/webhooks/XXXXXXXXXXXXXXXX\",\n            \"discord_communaute_zwave\": \"https://discord.com/api/webhooks/YYYYYYYYYYYYYYYY\",\n            \"discord_communaute_diy\": \"https://discord.com/api/webhooks/ZZZZZZZZZZZZZZZZZ\",\n        }\n    },\n    \"home_assistant\": {\n        \"dest\": {\n            \"tel_clement\": \"mobile_app_oneplus_a6010\",\n            \"tel_nathalie\": \"mobile_app_oneplus_a5000\"\n        }\n    },\n    \"email\": {\n        \"dest\": {\n            \"mail_clement\": \"email1@domain.fr\",\n            \"mail_john\": \"email2@domain.fr\",\n            \"mail_bibi\": \"email3@domain.fr\"        \n        }\n    },\n    \"telegram\": {\n        \"dest\": {\n            \"telegram_smarthome\": \"ID1\",\n            \"telegram_bot\": \"ID2\"        \n        }\n    },\n    \"mattermost\": {\n        \"dest\": {\n            \"mattermost_home\" : \"https://mattermost.domain.fr/hooks/XXXXXXXXXXXXXXXXXXXXXXXXX\",\n            \"mattermost_volets\" : \"https://mattermost.domain.fr/hooks/YYYYYYYYYYYYYYYYYYYYYYYY\",\n        }\n    },\n    \"sms\": {\n        \"url\": \"http://192.168.1.1:8080/?action=sendSms&number=##SMSNUMBER##&message=##MESSAGE##\",\n        \"dest\": {\n            \"sms_clement\": \"06XXXXXXXXX\",\n            \"sms_nathalie\": \"06YYYYYYYYY\",\n        }\n    }\n}\nglobal.set(\"notif\",configuration)\n\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "x": 700,
        "y": 260,
        "wires": [
            []
        ]
    },
    {
        "id": "513d2c9.069c3d4",
        "type": "change",
        "z": "a9ae8a01.8a5bd8",
        "name": "Remove old config",
        "rules": [
            {
                "t": "delete",
                "p": "notif",
                "pt": "global"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 470,
        "y": 260,
        "wires": [
            [
                "dacca689.055138"
            ]
        ]
    }
]
```
Et voici un exemple de flow d’input :

```
[{"id":"66da3fb7.1e4ff","type":"change","z":"8a8a63c5.0c3c2","name":"","rules":[{"t":"set","p":"title","pt":"msg","to":"Coucou","tot":"str"},{"t":"set","p":"message","pt":"msg","to":"Long Message","tot":"str"},{"t":"set","p":"short_message","pt":"msg","to":"Short Message","tot":"str"},{"t":"set","p":"dest","pt":"msg","to":"tel_clement;mail_clement","tot":"str"}],"action":"","property":"","from":"","to":"","reg":false,"x":740,"y":1880,"wires":[["7300bcc2.c97c34"]]}]
```

#Enjoy
