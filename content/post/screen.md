+++
date = "2015-09-24T16:32:52+02:00"
title = "Screen"
tags = [ "tools" ]
+++

```bash
screen          # start screen
screen -S name  # screate session with name
screen -L       # start screen and enable logging
screen -ls      # list pid's of reattached sessions
screen -r       # reattach session
screen -r 7849  # reattach session
screen –x       # attach  to  a not detached screen session
```

```
Ctrl a ? - help

Ctrl a d - deattach session
Ctrl a c - create new window
Ctrl a n - go to next window
Ctrl a p - go to previous window
Ctrl-a Ctrl-a - jump to most recently visited window
Ctrl a 4 - go to window #4
Ctrl a w - list all windows
Ctrl a " - show navigation menu
Ctrl a A - rename current window
Ctrl a x - lock session
Ctrl a k - kill curent window
Ctrl-a \ - kill all windows adn quit
Ctrl a K - kill session

Ctrl a H - start/stop logging
Ctrl a M - monitor window for activity
Ctrl a _ - monitor window for activity (silence or no output)

Ctrl a S - horizontal split
Ctrl-a | - vertical split
Ctrl-a [tab] - move between regions
Ctrl-a X - kill current region
Ctrl-a Q - go back to single region
```
