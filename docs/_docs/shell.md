---
layout: doc
title: The Shell
permalink: /docs/shell/

---

The shell can be used to manage features and settings in the application server.

# Entering the shell


The shell will display a welcome message.

```
   _____   _______    _____                            _               
  |_   _| |__   __|  / ____|                          | |              
    | |  ___ | |    | |     ___  _ __  _ __   ___  ___| |_ ___  _ __   
    | | / _ | |    | |    / _ | '_ | '_  / _ / __| __/ _ | '__|  
   _| || (_) | |    | |___| (_) | | | | | | |  __/ (__| || (_) | |     
  |________/|_|     ________/|_| |_|_| |_|___|___|_____/|_|     

        IDS Connector Console (1.1.0), Apache Karaf (4.1.2)

                       Fraunhofer AISEC 2018

Hit '<tab>' for a list of available commands
and '[cmd] --help' for help on a specific command.
Hit '<ctrl-d>' or 'osgi:shutdown' to shutdown Console.

karaf@root()>       
```

# Most important commands

List all installed features: `feature:list -i`

Display the logs: `log:tail -n 10000`