Dearning has various commands that you can try in your python terminal.

## dearning --stats
to find out whether dearning is compatible with your device, how big it is, and whether it is compatible with the version of Python you have.

example in smartphone
```command
/storage/emulated/0 $ dearning --stats
Ukuran dearning: 373.96 KB
Device: Linux
Python: 3.13.2 (compatible=True)
/storage/emulated/0 $
```

---

## dearning --help 
to give existing commands

example in smartphone
```command
/storage/emulated/0 $ dearning --help
usage: dearning [-h] [--task {classification,regression}]
                [--epochs EPOCHS] [--lr LR] [--stats]

🧠 Dearning CLI Interface

options:
  -h, --help            show this help message and exit
  --task {classification,regression}
  --epochs EPOCHS
  --lr LR
  --stats               Tampilkan status dearning (ukuran +
                        kompatibilitas)
```
or you can use the command dearning --h
```command
/storage/emulated/0 $ dearning --h
usage: dearning [-h] [--task {classification,regression}]
                [--epochs EPOCHS] [--lr LR] [--stats]

🧠 Dearning CLI Interface

options:
  -h, --help            show this help message and exit
  --task {classification,regression}
  --epochs EPOCHS
  --lr LR
  --stats               Tampilkan status dearning (ukuran +
                        kompatibilitas)
/storage/emulated/0 $
```