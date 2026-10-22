# Secure Chat Application with File Transfer

This is a secure chat application implemented in Python that allows multiple users to communicate with each other over a server. The application uses SSL/TLS for encrypted communication and supports basic functionalities like sending text messages, transferring files, listing active users, and performing administrative actions such as banning or kicking users.

## Features

- **SSL Encryption:** The chat is secure with SSL/TLS encryption.
- **Multi-client Support:** Multiple clients can connect to the server simultaneously.
- **User Nickname System:** Clients choose a nickname upon connection.
- **Active Users List:** Clients can request a list of currently connected users.
- **File Transfer:** Users can send files to each other via the `/file` command.
- **Admin Commands:** The server admin can kick or ban users by nickname.
- **Logging:** All messages and actions are logged in a history file.
- **Ban List:** Banned users are prevented from reconnecting until the ban is lifted.

## Requirements

- Python 3.x (standard library only — no packages to install)
- OpenSSL, to generate the TLS certificate

## How the encryption works

All traffic between clients and the server runs over **TLS** (Python's `ssl` module):

- The server wraps each accepted socket with `ssl.SSLContext(PROTOCOL_TLS_SERVER)` using `server-cert.pem` / `server-key.pem`.
- The client uses `ssl.create_default_context()`, trusts `server-cert.pem`, and **verifies the server's hostname** — so a client can't be tricked into talking to an impostor server.

## Setup

1. Generate a self-signed certificate. The client checks the hostname `vishwaas` (set in `client.py`), so the certificate must be issued for that name:

   ```bash
   openssl req -x509 -newkey rsa:4096 -nodes -days 365 \
     -keyout server-key.pem -out server-cert.pem \
     -subj "/CN=vishwaas" -addext "subjectAltName=DNS:vishwaas"
   ```

   To use a different name, change `server_hostname` in `client.py` and the `CN`/`DNS` values above to match.

2. Keep `server-key.pem` private — it is listed in `.gitignore` and must never be committed. Clients only need `server-cert.pem`.

3. Start the server, then one or more clients (each in its own terminal):

   ```bash
   python server.py
   python client.py
   ```

   The server listens on `127.0.0.1:55555`.

## Commands

**Client commands**

- `/file <filename> <recipient_nickname>` — send a file to another user
- `/users` — list all active users on the server
- `/quit` — disconnect from the server

**Admin commands (server-side)**

- `/kick <nickname>` — kick a user from the server
- `/ban <nickname>` — ban a user; banned users cannot reconnect

## File Transfer

Users can send files to one another using the `/file` command. The recipient receives the file in the `Received` directory on their machine.

## License

[MIT](LICENSE)

