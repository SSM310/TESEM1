import socket
import threading 
import time

PORT=8080

def run_server():
    
    server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    server_socket.bind(('0.0.0.0', PORT))
    server_socket.listen(1)
    print("Server listening on port", PORT)
    
    client_socket, addr = server_socket.accept()
    print("Connection from", addr)
    
    buffer = client_socket.recv(1024).decode()
    print(f"Server: {buffer}")
    
    hello = "Hello from server"
    client_socket.send(hello.encode())
    print("Hello message sent from server")
    
    client_socket.close()
    server_socket.close()

def run_client():
    time.sleep(1)  # Wait for the server to start
    client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    
    try:
        client_socket.connect(('127.0.0.1', PORT))
    except ConnectionRefusedError:
        print("Connection failed. Is the server running?")
        return
    
    hello = "Hello from client"
    client_socket.send(hello.encode())
    print("Client: Hello message sent")
    
    buffer = client_socket.recv(1024).decode()
    print(f"Client: {buffer}")
    
    client_socket.close()

server_thread = threading.Thread(target=run_server)
server_thread.start()

time.sleep(1)  # Allow time for the server to start

run_client()
server_thread.join()



