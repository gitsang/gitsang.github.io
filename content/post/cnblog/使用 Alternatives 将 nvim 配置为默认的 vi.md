我最早曾经直接将 vim 命名为 vi 或 nvim 命名为 vi 来替换 vi 命令打开的程序。

```
sudo update-alternatives --install /usr/bin/vi vi "/usr/bin/nvim" 100
```

```
# update-alternatives --config vi
There are 3 choices for the alternative vi (providing /usr/bin/vi).

  Selection    Path                Priority   Status
------------------------------------------------------------
* 0            /usr/bin/nvim        100       auto mode
  1            /usr/bin/nvim        100       manual mode
  2            /usr/bin/vim.basic   30        manual mode
  3            /usr/bin/vim.tiny    15        manual mode

Press <enter> to keep the current choice[*], or type selection number:
```