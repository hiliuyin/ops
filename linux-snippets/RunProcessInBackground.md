
#### nohup
```nohup java -jar ~/test.jar &```

```nohup node server.js > /dev/null 2>&1 &```

#### screen
```
screen -list
There are screens on:
	74083.ttys005.liuyindeMacBook-Pro	(Detached)
	74004.ttys005.liuyindeMacBook-Pro	(Detached)
2 Sockets in /var/folders/dv/_txn4cw57pq7s75j0q3yk4sr0000gn/T/.screen.
```
- Detach: ctrl+a+d
- Retach: screen -r 74083.ttys005.liuyindeMacBook-Pro

Refer to:
https://stackoverflow.com/questions/4797050/how-to-run-process-as-background-and-never-die
