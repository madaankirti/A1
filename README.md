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
  
## High Level idea of various functions
| Function                                                                 | Purpose                                                        |
| ------------------------------------------------------------------------ | -------------------------------------------------------------- |
| `handle_client(int client_socket)`                                        | Manages client authentication and message handling.            |
| `client_authenticate(int client_socket, std::string &username)`          | Authenticates users from `users.txt`.                           |
| `send_private_message(const std::string &sender, const std::string &command, int client_socket)` | Handles private messaging.                                      |
| `send_group_message(const std::string &sender, int sender_socket, const std::string &command)`   | Sends a message to all members of a group.                      |
| `broadcast_to_clients(const std::string &message, int sender_socket)`     | Broadcasts messages to all users.                               |

### Explanation of Flow:
1. **Server Initialization:**
   - The server reads `users.txt` for authentication details.
   - It then creates a TCP socket, binds to a port, and listens for incoming client connections.

2. **Client Connection & Authentication:**
   - Once a connection is detected, the server creates a new thread to handle the client.
   - The client is prompted to enter a username and password, which are validated.

3. **Message Processing:**
   - If the credentials are valid, the server listens for commands such as `/msg`, `/broadcast`, or `/group_msg`.
   - Based on the received command, the server executes the corresponding function.

4. **Client Disconnection Handling:**
   - If the client disconnects, they are removed from the active users list.
   - The server notifies other clients of the disconnection.

5. **Invalid Authentication:**
   - If the username or password is invalid, the server notifies the client and asks for the credentials again.

## Testing

### 1. **Correctness Testing**

The following functionalities have been verified:

- **Client Connection & Authentication**: Clients can successfully connect, authenticate, and send messages.
- **Message Delivery**: Both private and group messages are delivered correctly to the intended recipients.
- **Pre-authentication Message Restriction**: Users are not allowed to send messages before completing the authentication process.

### 2. **Stress Testing**

To ensure the system can handle high loads, the following stress tests were conducted:

- **Concurrent Clients**: Simulated multiple concurrent clients to evaluate thread stability and server performance.
- **Heavy Message Load**: Tested the server's ability to handle a large volume of messages under stress.

### 3. **Edge Cases Handled**

Edge cases were tested to ensure proper handling of various scenarios. Below are the scenarios and their expected behaviors:

| **Scenario**                            | **Expected Behavior**                             |
|-----------------------------------------|--------------------------------------------------|
| Sending a message to a non-existent user | An error message is displayed to the sender.     |
| Trying to join a non-existent group     | An error message is displayed to the user.       |
| Sending messages before authentication  | The connection is closed and access is denied.   |
| User disconnects while in a group       | The user is removed from the group list.         |

## Challenges

1. To handle concurrent clients, we needed to manage simultaneous access to shared data structures like clients and groups. To prevent race conditions, `std::mutex` was used to ensure safe access and maintain data consistency.

2. Debugging multi-threaded code was challenging due to the unpredictable execution order of threads. To address this, extensive logging with `std::cerr` was implemented, allowing us to track execution flow and identify potential issues more effectively.

3. Parsing user commands required extracting parameters such as `group_name` and `username`. This was accomplished using `std::string::find()` and `std::string::substr()`, ensuring accurate parsing while maintaining efficiency.

## Individual Contributions

| Member Name  | Contribution (%) | Tasks Completed                          |
|--------------|------------------|-------------------------------------------|
| Amandeep Upadhyay (210114)       | 33%              | Implemented core server logic including client handling, authenication and multithreading |
| Kirti (210522)       | 33%              | Developed messaging features,private,group and broadcast messaging, debugging       |
| Mohammad Suhail Ilyas (210617)      | 33%              | Testing, stress testing, documentation and debugging edge cases      |
