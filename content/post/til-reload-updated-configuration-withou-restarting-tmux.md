+++
author = "Tan Linh"
date = 2017-03-29T10:19:00Z
description = "Mặc định, tmux sẽ đọc cấu hình tại đường dẫn ~/.tmux.conf trừ phi nó chưa running. Vì thế, sau khi tọc mạch phải reload config cho nó. "
draft = false
slug = "til-reload-updated-configuration-withou-restarting-tmux"
tag = ["TIL","Linux"]
title = "TIL: Reload updated configuration without restarting tmux"

+++

Mặc định, `tmux` sẽ đọc cấu hình tại đường dẫn `~/.tmux.conf` trừ phi nó chưa running. Vì thế, sau khi tọc mạch phải reload config cho nó. 
Like this:
```shell
tmux source-file ~/.tmux.conf
```

Ngoài ra, bạn có thể bind key để reload cho tiện.

```shell
bind-key R source-file ~/.tmux.conf
```

Hoặc bên trong `tmux` typing `Ctrl a :source ~/.tmux.conf`

> Ở đây mình bind `Ctrl a` thay cho `Ctrl B`

tanlinhnd, 29-03-2017
