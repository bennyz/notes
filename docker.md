Remove dead containers
```  
  docker rm $(docker ps -qa --filter status=exited)
  ```
