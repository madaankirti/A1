# A1
## Features  
### Implemented Features  
1. **Multi-threaded TCP server**  
2. **User authentication** using `users.txt`  
3. **Group functions:**  
   - Create group  
   - Join group  
   - Leave group  
4. **Private messaging**  
5. **Group messaging**  
6. **Broadcast messaging**  

## Design Decisions  
### Multi-threading  
Used **one thread per client** instead of creating a new process per client.  
- Threads consume less memory 
- Reduces context-switching overhead 
- No need for Inter-Process Communication (IPC) 
