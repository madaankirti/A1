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
### Data Structures Used
| Data Structure                                   | Function                                    | Reason                                   |
| ------------------------------------------------ | --------------------------------------------| ---------------------------------------- |
| `std::unordered_map<int, std::string> clients`   | Maps client sockets to usernames            | Fast lookup of clients                   |
| `std::unordered_map<std::string, int> usernames` | Maps usernames to client sockets            | Enables direct messaging                 |
| `std::unordered_map<std::string, std::unordered_set<int>> groups` | Stores group names and their members | Fast lookup for group messaging          |
| `std::mutex clients_list_mutex`                  | Synchronizes access to shared data          | Prevents race conditions                 |
| `std::mutex log_mutex`                           | Ensures ordered logging output              | Avoids mixed console output              |
  
