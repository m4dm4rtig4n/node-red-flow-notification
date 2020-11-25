# node-red-flow-notification

Liens vers le flow : [ici](export.flow)

Hello,

Après avoir passer quelques heures sur mon système de notification multi-channel, je me dit que ça pourrais être cool partager mon subflow :wink:

Il est encore en cours de peaufinage, mais va déjà vous permettre de centralisé toutes vos notifications.

Actuellement, il gérée :

- HTML5 via Home Assistant
- Discord
- Mattermost
- Home Assistant (application mobile)
- Email
- Telegram
- TTS
- SMS via JPI
- Awtrix

Petit screen :

![Screen](2020-10-30_09-34.png)

Je vous joins également la petite doc que j’ai intégrée dans le subflow :

----

**Information**

Ce subflow va vous permettre de notifier plusieurs service en même temps.

Actuellement il gérée les services :

- HTML5 (by Home Assistant)
- Discord
- Mattermost
- Telegram
- Email
- TTS
- Home Assistant (Mobile App)

**Pré-requis**

- node-red-contrib-string
- node-red-contrib-home-assistant-websocket
- node-red-node-email
- node-red-contrib-telegrambot

**Entrée**

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

Tout les inputs ne sont pas forcement compatible avec les differents services.

**Titre** / *msg.title*

*Disponible sur : Email*

Titre du mail.

*Exemple:*
```
msg.title = "Home Assistant - New version available"
````


**Message** / *msg.message*

*Disponible sur : Email / Telegram / Discord*

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

**Image** / *msg.image*

*Disponible sur : Telegram*

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

_Exemple:_
```
msg.dest = "sms_clement"
```

**Awtrix**
dest doit commencer par `awtrix_`

_Exemple:_
```
msg.dest = "awtrix_home"
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
    "jpi": {
        "address": "192.168.1.1:8080",
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
    }
}
global.set("notif",configuration)
```

Pour vous aidez dans la configuration voici une function qui va vous permettres de pousser votre configuration à chaque deploiement.

Tout ce passe dans le node Function.

```
[{"id":"bbda9cf1.ad8ba","type":"inject","z":"4787bdd6.9aa874","name":"","props":[{"p":"payload"}],"repeat":"","crontab":"","once":true,"onceDelay":0.1,"topic":"","payload":"","payloadType":"date","x":130,"y":130,"wires":[["513d2c9.069c3d4"]]},{"id":"dacca689.055138","type":"function","z":"4787bdd6.9aa874","name":"Configurateur EXEMPLE","func":"var configuration = \n{\n    \"discord\": {\n        \"dest\": {\n            \"discord_communaute_news\": \"https://discord.com/api/webhooks/XXXXXXXXXXXXXXXX\",\n            \"discord_communaute_zwave\": \"https://discord.com/api/webhooks/YYYYYYYYYYYYYYYY\",\n            \"discord_communaute_diy\": \"https://discord.com/api/webhooks/ZZZZZZZZZZZZZZZZZ\",\n        }\n    },\n    \"home_assistant\": {\n        \"dest\": {\n            \"tel_clement\": \"mobile_app_oneplus_a6010\",\n            \"tel_nathalie\": \"mobile_app_oneplus_a5000\"\n        }\n    },\n    \"email\": {\n        \"dest\": {\n            \"mail_clement\": \"email1@domain.fr\",\n            \"mail_john\": \"email2@domain.fr\",\n            \"mail_bibi\": \"email3@domain.fr\"        \n        }\n    },\n    \"telegram\": {\n        \"dest\": {\n            \"telegram_smarthome\": \"ID1\",\n            \"telegram_bot\": \"ID2\"        \n        }\n    },\n    \"mattermost\": {\n        \"dest\": {\n            \"mattermost_home\" : \"https://mattermost.domain.fr/hooks/XXXXXXXXXXXXXXXXXXXXXXXXX\",\n            \"mattermost_volets\" : \"https://mattermost.domain.fr/hooks/YYYYYYYYYYYYYYYYYYYYYYYY\",\n        }\n    },\n    \"jpi\": {\n        \"address\": \"192.168.1.1:8080\",\n        \"dest\": {\n            \"sms_clement\": \"06XXXXXXXXX\",\n            \"sms_nathalie\": \"06YYYYYYYYY\",\n        }\n    }\n}\nglobal.set(\"notif\",configuration)\n\nreturn msg;","outputs":1,"noerr":0,"initialize":"","finalize":"","x":540,"y":130,"wires":[[]]},{"id":"513d2c9.069c3d4","type":"change","z":"4787bdd6.9aa874","name":"Remove old config","rules":[{"t":"delete","p":"notif","pt":"global"}],"action":"","property":"","from":"","to":"","reg":false,"x":310,"y":130,"wires":[["dacca689.055138"]]}]
```
Et voici un exemple de flow d’input :

```
[{"id":"66da3fb7.1e4ff","type":"change","z":"8a8a63c5.0c3c2","name":"","rules":[{"t":"set","p":"title","pt":"msg","to":"Coucou","tot":"str"},{"t":"set","p":"message","pt":"msg","to":"Long Message","tot":"str"},{"t":"set","p":"short_message","pt":"msg","to":"Short Message","tot":"str"},{"t":"set","p":"dest","pt":"msg","to":"tel_clement;mail_clement","tot":"str"}],"action":"","property":"","from":"","to":"","reg":false,"x":740,"y":1880,"wires":[["7300bcc2.c97c34"]]}]
```

#Enjoy