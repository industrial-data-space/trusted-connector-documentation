---
layout: doc
title: The Shell
permalink: /docs/shell/

---

# Deprecated

> Spring Boot uses a different system. This will need to be updated.

The shell can be used to manage features and settings in the application server.

# Entering the shell


The shell will display a welcome message.

```
Listening for transport dt_socket at address: 5005
  _____               _           _    ____                            _ 
 |_   _| __ _   _ ___| |_ ___  __| |  / ___|___  _ __  _ __   ___  ___| |_ ___  _ __ 
   | || '__| | | / __| __/ _ \/ _` | | |   / _ \| '_ \| '_ \ / _ \/ __| __/ _ \| '__| 
   | || |  | |_| \__ \ ||  __/ (_| | | |__| (_) | | | | | | |  __/ (__| || (_) | | 
   |_||_|   \__,_|___/\__\___|\__,_|  \____\___/|_| |_|_| |_|\___|\___|\__\___/|_| 

           Trusted Connector Console (3.0.0-SNAPSHOT), Apache Karaf (4.2.7) 

                                 Fraunhofer AISEC 2020 

Hit '<tab>' for a list of available commands
and '[cmd] --help' for help on a specific command.
Hit '<ctrl-d>' or 'system:shutdown' to shutdown Console.

karaf@root()>
```

# Most important commands

List all installed features: `feature:list -i`

Display the logs: `log:tail -n 10000`