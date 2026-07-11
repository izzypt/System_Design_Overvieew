# System_Design_Overvieew


1 - [CAP Theorem](#cap-theorem-section)

2 - [OSI Model](#osi-model)

3 - [TCP/IP Model](#tcp/ip-model)

4 - [ACID and SQL/NoSQL](#acid-sql-nosql)

5 - [Communication in Real-Time - WebSockets](#websockets)


<a name="cap-theorem-section"><a/>
# 1 - CAP Theorem

<img width="2048" height="1933" alt="image" src="https://github.com/user-attachments/assets/a1d80d2e-48a5-43e6-9e76-96b8b373ee94" />

The CAP theorem is a fundamental rule in system design. It states that a distributed data store (a system made of multiple machines working together) can only simultaneously provide two out of three guarantees: Consistency, Availability, and Partition Tolerance.

Here is what those three letters actually mean in plain English:

- Consistency (C): All nodes display identical data, guaranteeing that reads always reflect the most recent write.
Every read receives the most recent write or an error. Imagine updating your profile picture; if the system is consistent, anyone checking your profile a millisecond later will see the new picture, not the old one.

- Availability (A): Every request receives a response, without guarantee that it contains the most recent write. Every non-failing node returns a non-error response, without the guarantee that it contains the most recent write. The system is always up and responsive, even if it occasionally serves stale data.

- Partition Tolerance (P): The system continues to operate despite network partitions.
The system continues to operate despite an arbitrary number of messages being dropped or delayed by the network between nodes.

<img width="800" height="487" alt="image" src="https://github.com/user-attachments/assets/76ab1927-d556-41fd-8e26-8842661de877" />

<a name="osi-model"><a/>
# 2 - OSI Model

Think of the OSI (Open Systems Interconnection) model as a **conceptual universal translator** for networking. It breaks down how data travels from an app on your device (like a web browser) all the way across the world through cables, and up into another app.

The model splits this massive job into **7 distinct layers**. When you send data, it travels from Layer 7 down to Layer 1. When you receive data, it moves from Layer 1 up to Layer 7.

<img width="1363" height="2048" alt="image" src="https://github.com/user-attachments/assets/957d8a22-16ad-468a-a460-ee837c7ccf85" />


### The 7 Layers Explained (Top to Bottom)

To remember the order from 7 down to 1, network engineers use the phrase: **A**ll **P**eople **S**eem **To** **N**eed **D**ata **P**rocessing.

#### 7. Application Layer

This is the layer interacting directly with your software application. Your browser doesn't live here, but the network protocols it uses do.

* **What it does:** Provides network services directly to end-user applications.
* **Protocols / Tech:** HTTP, HTTPS, FTP, SMTP (email), DNS.

#### 6. Presentation Layer

This layer acts as the translator. It ensures that the data sent by Layer 7 is formatted in a readable way for the receiver.

* **What it does:** Data formatting, syntax translation, compression, and encryption/decryption (like SSL/TLS).
* **Protocols / Tech:** JPEG, MP3, ASCII, SSL/TLS.

#### 5. Session Layer

This is the "mechanic" that opens, manages, and closes the communication channel between two devices.

* **What it does:** Sets up, coordinates, and terminates conversations (sessions) between applications.
* **Protocols / Tech:** NetBIOS, RPC, SOCKS.

#### 4. Transport Layer

This layer is responsible for end-to-end communication and breaking large files into smaller chunks called **segments**.

* **What it does:** Flow control (not sending faster than a receiver can handle) and error control (checking if data arrived corrupted).
* **Protocols / Tech:** TCP (reliable, slow), UDP (unreliable, fast).

#### 3. Network Layer

This layer is all about finding the path to move data between different networks (routing). Data chunks here are called **packets**.

* **What it does:** Handles logical addressing (IP addresses) and routes packets across various interconnected networks.
* **Protocols / Tech:** IP (IPv4, IPv6), Routers, ICMP (ping).

#### 2. Data Link Layer

While the Network layer moves data *between* networks, the Data Link layer moves data between devices on the *same* local network. Data here is called **frames**.

* **What it does:** Handles physical addressing (MAC addresses) and fixes errors from the physical layer.
* **Protocols / Tech:** Ethernet, Wi-Fi (802.11), Network Switches.

#### 1. Physical Layer

The actual hardware. This layer deals with the raw electrical, optical, or radio signals traveling across physical media. Data here is just raw **bits** (0s and 1s).

* **What it does:** Transmits raw bitstreams over a physical medium.
* **Protocols / Tech:** Cables (Cat6, Fiber optic), Hubs, Modems.

---

### How Data Flows: The Post Office Analogy

Imagine writing a letter (Data) to a friend:

1. **Layer 7-5:** You write the letter (Application), translate it to English (Presentation), and log into your desk to start the task (Session).
2. **Layer 4 (Transport):** Your letter is too long for one envelope, so you split it into 3 pages, label them "1 of 3", "2 of 3", and "3 of 3", and include instructions on how to put them back together.
3. **Layer 3 (Network):** You place each page into an envelope and write your friend's global mailing address (IP Address) on them.
4. **Layer 2 (Data Link):** The post office looks at the zip code and puts the envelopes onto a specific local delivery truck (MAC Address / Switch).
5. **Layer 1 (Physical):** The truck physically drives down the asphalt road (Cables/Wires) to deliver the letters.



```text
+-----------------------------------------------------------------------+
|  LAYER 3 WRAPPER (IP Header)                                          |
|  [Source: 192.168.1.5 | Destination: 8.8.8.8]                         |
|                                                                       |
|   +---------------------------------------------------------------+   |
|   |  LAYER 4 WRAPPER (TCP Header)                                 |   |
|   |  [Source Port: 51234 | Dest Port: 443 | Seq: Page 1 of 3]     |   |
|   |                                                               |   |
|   |   +-------------------------------------------------------+   |   |
|   |   |  THE ACTUAL DATA                                      |   |   |
|   |   |  "Dear friend, I am writing to..."                    |   |   |
|   |   +-------------------------------------------------------+   |   |
|   +---------------------------------------------------------------+   |
+-----------------------------------------------------------------------+
\_______________________________________________________________________/
                                This entire bundle is a PACKET
    \_______________________________________________________________/
                     The inside nested portion is a SEGMENT
```



### Layer 7 (Application) — Writing the Letter

* *layer 7 - writing the letter ? http body and content to send over to some other pc ?*
* **Improvement:** This is you typing your message into an app. The app creates the raw **HTTP Body and headers** (the actual content, like a JSON payload or HTML page) meant for the destination computer.

### Layer 6 (Presentation) — Translating & Encrypting

* *layer 6 - translate and encrypt the letter to make sure only the receiver is able to read it...*
* **Improvement:** This is where your plaintext letter gets translated into standard character sets (like UTF-8) and **encrypted using TLS/SSL**. It ensures that if someone steals the letter in transit, it just looks like randomized gibberish.

### Layer 5 (Session) — Sealing & Addressing the Envelope

* **Added addition:** This is the act of **opening the mailbox session**. It establishes a dedicated connection ("session") between your app and the server so they know they are actively talking to each other.

### Layer 4 (Transport) — Paging & Numbering (The Segments)

* *4 - Break down the letter into pages and add tcp header ?*
* **Improvement:** If the letter is too big, TCP cuts it into chunks (**Segments**). It numbers them ("Page 1 of 3", "Page 2 of 3") so the receiving computer can put them back in the right order, and adds the **Port Numbers** so the target PC knows which app gets the letter.

### Layer 3 (Network) — The Global Mailing Address (The Packets)

* *3 - Wrap the letter and pages into a IP header, with destination of letter ...*
* **Improvement:** Every individual page gets stuffed into its own envelope (**Packet**). Layer 3 writes the **Source IP** and **Destination IP** on the outside. This allows the global post office (the internet's core routers) to look at the envelope and pass it across the world.

### Layer 2 (Data Link) — Handing it to the Local Postman (The Frames)

* *2 - data/network layer... send the letter to the postman (router) which will send it to another pc in another network ?*
* **Improvement:** You have the right intuition, but let's sharpen the hardware distinction. Layer 3 handles the *global* address (IP), but **Layer 2 handles the *local* hop**. It puts the packet inside a **Frame** stamped with your local router's hardware address (**MAC Address**). It's like handing the letter to your neighborhood mail carrier to take it to the sorting facility.

### Layer 1 (Physical) — The Road It Travels On

* **Added addition:** The actual asphalt, trucks, and tracks. This layer converts the digital Frame into raw electrical voltages, fiber-optic light pulses, or Wi-Fi radio waves to physically shoot the data across the wires.

<a name="tcp/ip-model"><a/>
# 3 - TCP/IP Model

While the 7-layer **OSI model** is the gold standard for teaching networking concepts, the **TCP/IP model** is what the internet actually runs on.

The easiest way to think about it is that the OSI model is a **theoretical** framework designed by a committee, whereas the TCP/IP model is a **practical** framework designed by engineers to get computers talking immediately.

---

## The Core Difference: Layer Merging

The TCP/IP model combines several of the OSI layers because, in practice, software developers usually handle them all together. It condenses the 7 layers down into **4 layers**.

| OSI Layer | TCP/IP Layer | What lives there? |
| --- | --- | --- |
| **7. Application**<br>

<br>**6. Presentation**<br>

<br>**5. Session** | **4. Application** | Everything the user interacts with (HTTP, FTP, SMTP, DNS, encryption/TLS, and session management). |
| **4. Transport** | **3. Transport** | Controls host-to-host data flow (TCP, UDP). |
| **3. Network** | **2. Internet** | Routes packets across different networks globally (IP addresses, ICMP). |
| **2. Data Link**<br>

<br>**1. Physical** | **1. Network Access** *(or Link Layer)* | Handles physical hardware, local wiring, and local device jumping (Ethernet, Wi-Fi, MAC addresses). |

---

## Structural & Philosophical Differences

### 1. Theory vs. Practice

* **OSI (Open Systems Interconnection):** Created by the International Organization for Standardization (ISO) in 1984. It was designed *before* the protocols were actually written. Because it was built by a committee, it is highly structured and strict, but sometimes clunky.
* **TCP/IP (Transmission Control Protocol / Internet Protocol):** Developed by the US Department of Defense (DARPA) in the 1970s. It was built around existing protocols that were already working in the real world.

### 2. Loose vs. Rigid Boundaries

* **In OSI,** the boundaries are strict. An application developer *cannot* touch Layer 5 or 6 directly; they must pass data down through the rigid chain.
* **In TCP/IP,** the Application layer is a catch-all. If you are writing a web application, your code handles the data (OSI Layer 7), handles the JSON serialization (OSI Layer 6), and tracks the login session (OSI Layer 5) all inside the same program.

### 3. Connection Philosophy

* **OSI model** supports both connectionless and connection-oriented communication at the Network layer.
* **TCP/IP model** assumes a connectionless Network layer (IP packets are just thrown into the internet, and routers try their best to deliver them). It shifts the responsibility of a reliable connection entirely up to the Transport layer (TCP).

## Which one should you use?

* Use the **OSI model** when you are **troubleshooting or interviewing**. (e.g., *"We have a Layer 3 issue"* means check the router or IP configurations; *"We have a Layer 1 issue"* means check if the cable is plugged in).
* Use the **TCP/IP model** when you are **building software or configuring production servers**, as it maps directly to how the actual protocols and operating system kernels interact.


<a name="acid-sql-nosql"><a/>
# 4 - ACID and SQL/NoSQL

Here is the complete breakdown of how databases manage reliability (ACID), how they are built (SQL vs. NoSQL), and how to choose the right one for your project.

---

## Part 1: What is ACID?

**ACID** is a set of four safety properties that guarantee database transactions are processed reliably. If a database is ACID-compliant, you can trust that your data won't get corrupted, even if the power goes out mid-transaction.

* **Atomicity ("All or Nothing"):** A transaction often requires multiple steps. Atomicity guarantees that either *all* steps succeed, or the entire transaction fails and rolls back like nothing happened.
* *Example:* If you transfer $100 from Account A to Account B, the money must be debited from A *and* credited to B. If the server crashes halfway through, the database rolls back so Account A doesn't lose $100 into thin air.


* **Consistency ("Follow the Rules"):** A transaction can only take the database from one valid state to another, maintaining all schema rules, constraints, and data types.
* *Example:* If a database column states that a user balance cannot be negative, any transaction that would result in a negative balance is rejected.


* **Isolation ("No Peeking"):** If multiple users are making changes at the exact same time, their transactions are isolated from one another. The database handles them in a way that makes them feel sequential.
* *Example:* If two people buy the very last concert ticket at the exact same millisecond, the database isolates the transactions so only one succeeds, preventing double-selling.


* **Durability ("Built to Last"):** Once a transaction is committed, it is permanently saved in non-volatile memory (like a hard drive). Even if the entire data center loses power a millisecond later, the data is safe.

---

## Part 2: SQL vs. NoSQL (The Core Differences)

The easiest way to understand the difference is how they organize data: **SQL is like an Excel Spreadsheet; NoSQL is like a folder full of JSON text files.**

| Feature | SQL (Relational) | NoSQL (Non-Relational) |
| --- | --- | --- |
| **Data Model** | Strict tables with fixed rows and columns. | Flexible (JSON documents, Key-Value pairs, Graphs, Column-families). |
| **Schema** | **Static.** You must define your tables and columns *before* adding data. Altering a schema later is heavy work. | **Dynamic.** Every row (document) can have completely different fields. You can add new fields on the fly. |
| **Scaling** | **Vertical.** You scale by buying a bigger, faster server (more CPU/RAM). | **Horizontal.** You scale by adding *more* cheap servers to a cluster (sharding data across them). |
| **ACID Support** | Native, out-of-the-box ACID compliance. | Historically prioritized performance over ACID (relies on *Eventual Consistency*), though many modern NoSQL DBs offer limited ACID support now. |

---

## Part 3: Where Each One Shines

### Choose SQL if...

You have highly structured data where relationships matter immensely, and data integrity is non-negotiable.

* **Fintech & E-Commerce:** Apps handling money, ledgers, or inventories where a single missing row or calculation error ruins the business.
* **Complex Analytical Queries:** Systems where you need to join dozens of tables together to generate deeply specific reports (e.g., *"Show me all users who bought product X in July who live in Lisbon"*).
* **Popular choices:** PostgreSQL, MySQL, SQLite, Microsoft SQL Server.

### Choose NoSQL if...

You are dealing with massive amounts of unstructured data, rapidly changing features, or need massive horizontal scale.


* **Real-time Analytics & Logging:** Tracking millions of user clicks, IoT sensor data, or system logs where speed is critical and missing a single data point won't break the app.
* **Content Management & Social Feeds:** User profiles where different users have entirely different attributes (e.g., one user has a LinkedIn link, another has a bio, another has nothing).
* **Rapid Prototyping:** Startups changing their data structure every week without wanting to run complex database migrations.
* **Popular choices:** MongoDB (Document), Redis (Key-Value), Neo4j (Graph), Cassandra (Wide-Column).


<a name="websockets"><a/>
# 5 - Communication in Real-Time

<img width="2235" height="3192" alt="image" src="https://github.com/user-attachments/assets/030d9af9-8b6f-4700-9a95-350f122c39cb" />

## O problema que os WebSockets resolvem

Antes dos WebSockets existirem, se quisesses dados em "tempo real" no browser tinhas duas más opções:

- **Polling**: o cliente faz `GET /updates` a cada X segundos. Simples, mas desperdiça pedidos quando não há nada de novo, e introduz latência (na pior das hipóteses, esperas quase X segundos por uma atualização).
- **Long polling**: o cliente faz um pedido HTTP que o servidor mantém aberto até ter algo para responder. Reduz o desperdício, mas cada resposta implica reabrir uma nova ligação logo a seguir, com todo o overhead de headers HTTP outra vez.

Os WebSockets resolvem isto com uma ligação TCP persistente e bidirecional: depois de estabelecida, tanto o cliente como o servidor podem enviar mensagens a qualquer momento, sem reabrir nada.

## O handshake: de HTTP para WebSocket

Uma ligação WebSocket começa sempre como um pedido HTTP normal. O cliente pede um "upgrade" de protocolo:

```
GET /chat HTTP/1.1
Host: exemplo.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Version: 13
```

Se o servidor aceitar, responde com `101 Switching Protocols` e a partir daí a ligação TCP deixa de falar HTTP e passa a falar o protocolo WebSocket:

```
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
```

O `Sec-WebSocket-Accept` não é aleatório — é calculado a partir do `Sec-WebSocket-Key` do cliente, concatenado com um GUID fixo definido na RFC 6455, seguido de SHA-1 e Base64. Isto serve para confirmar que quem respondeu percebe mesmo o protocolo WebSocket (e não é, por exemplo, uma cache HTTP mal configurada a devolver uma resposta antiga). Em Python:

```python
import hashlib
import base64

WEBSOCKET_MAGIC_STRING = "258EAFA5-E914-47DA-95CA-C5AB0DC85B11"

def compute_accept_key(client_key: str) -> str:
    sha1 = hashlib.sha1((client_key + WEBSOCKET_MAGIC_STRING).encode())
    return base64.b64encode(sha1.digest()).decode()

compute_accept_key("dGhlIHNhbXBsZSBub25jZQ==")
# 's3pPLMBiTxaQ9kYGzzhZRbK+xOo='
```

Na prática nunca vais implementar isto manualmente — bibliotecas como `websockets` ou frameworks como FastAPI tratam do handshake por ti. Mas perceber que é "só" um pedido HTTP com uma verificação criptográfica leve ajuda a entender por que é que os WebSockets funcionam através da maior parte de proxies e load balancers modernos (é literalmente HTTP até este ponto).

## Anatomia de um frame

Depois do handshake, os dados já não viajam como texto HTTP — viajam como **frames binários**. Cada frame tem, resumidamente:

- **FIN bit**: indica se este é o último frame de uma mensagem (mensagens podem ser fragmentadas em vários frames).
- **Opcode**: o tipo de frame — `0x1` texto, `0x2` binário, `0x8` close, `0x9` ping, `0xA` pong.
- **MASK bit + masking key**: frames enviados pelo cliente têm de ser mascarados (XOR com uma chave de 4 bytes) por razões de segurança — evita que dados de cliente pareçam tráfego HTTP arbitrário a proxies mal configurados. O servidor nunca mascara.
- **Payload length**: pode ser codificado em 7, 16 ou 64 bits, dependendo do tamanho.

Para perceberes a mecânica, aqui está uma descodificação minimalista de um frame de texto não fragmentado, feita à mão sobre um socket raw (só para fins didáticos — nunca farias isto em produção):

```python
import struct

def decode_frame(data: bytes) -> str:
    b1, b2 = data[0], data[1]
    opcode = b1 & 0x0F
    masked = (b2 & 0x80) != 0
    payload_len = b2 & 0x7F

    offset = 2
    if payload_len == 126:
        payload_len = struct.unpack(">H", data[offset:offset+2])[0]
        offset += 2
    elif payload_len == 127:
        payload_len = struct.unpack(">Q", data[offset:offset+8])[0]
        offset += 8

    if masked:
        mask_key = data[offset:offset+4]
        offset += 4
        payload = data[offset:offset+payload_len]
        payload = bytes(b ^ mask_key[i % 4] for i, b in enumerate(payload))
    else:
        payload = data[offset:offset+payload_len]

    return payload.decode("utf-8") if opcode == 0x1 else payload
```

Na prática usas sempre uma biblioteca para isto — o objetivo aqui é só desmistificar o que está a acontecer "por baixo" quando chamas `websocket.send("olá")`.

## Um servidor WebSocket simples com Python

A biblioteca [`websockets`](https://websockets.readthedocs.io/) é a forma mais direta de trabalhar com WebSockets em Python puro, sem framework:

```python
import asyncio
import websockets

async def echo(websocket):
    async for message in websocket:
        print(f"Recebido: {message}")
        await websocket.send(f"Echo: {message}")

async def main():
    async with websockets.serve(echo, "localhost", 8765):
        await asyncio.Future()  # corre para sempre

asyncio.run(main())
```

E o cliente correspondente:

```python
import asyncio
import websockets

async def client():
    async with websockets.connect("ws://localhost:8765") as ws:
        await ws.send("olá servidor")
        resposta = await ws.recv()
        print(resposta)

asyncio.run(client())
```

Repara que não há loop manual de "faz pedido, espera resposta, repete" — a ligação fica aberta e `send`/`recv` funcionam a qualquer momento, em qualquer direção.

## Ping/Pong e fecho da ligação

Ligações TCP podem morrer silenciosamente (um router desliga, o portátil vai para suspensão). O protocolo WebSocket define frames `ping` e `pong` como heartbeat: o servidor (ou cliente) envia um `ping` periodicamente e espera um `pong` de volta; se não chegar dentro de um timeout, considera a ligação morta e fecha-a.

A biblioteca `websockets` faz isto automaticamente por defeito, mas é configurável:

```python
async with websockets.serve(
    echo,
    "localhost",
    8765,
    ping_interval=20,  # envia ping a cada 20s
    ping_timeout=10,   # fecha se não houver pong em 10s
):
    ...
```

O fecho da ligação também é um handshake próprio: um lado envia um frame `close` (opcode `0x8`, opcionalmente com um código de razão), o outro responde com o seu próprio `close`, e só depois o socket TCP subjacente é fechado. Isto evita perder mensagens que já estavam "em trânsito" quando alguém decide desligar.

## WebSockets num framework real: FastAPI

No teu stack (FastAPI), o suporte a WebSockets é nativo via Starlette:

```python
from fastapi import FastAPI, WebSocket, WebSocketDisconnect

app = FastAPI()

@app.websocket("/ws/chat/{room_id}")
async def chat_endpoint(websocket: WebSocket, room_id: str):
    await websocket.accept()
    try:
        while True:
            data = await websocket.receive_text()
            await websocket.send_text(f"[{room_id}] {data}")
    except WebSocketDisconnect:
        print(f"Cliente saiu da sala {room_id}")
```

`websocket.accept()` é onde o handshake HTTP → WS acontece de facto (podias inspecionar/validar headers antes de aceitar, por exemplo para autenticação). `receive_text()` e `send_text()` bloqueiam a corrotina até haver dados, sem polling.

## O problema real: escalar para múltiplos processos

Isto é o que normalmente apanha as pessoas de surpresa. Um único processo FastAPI/Uvicorn consegue manter, digamos, milhares de ligações WebSocket abertas em memória — mas assim que corres **múltiplos workers** (o normal em produção, com Gunicorn/Uvicorn `--workers 4` ou várias réplicas em Kubernetes), cada worker só conhece as ligações que ele próprio aceitou.

Se o utilizador A está ligado ao worker 1 e o utilizador B ao worker 2, e A quer mandar uma mensagem a B, o worker 1 não tem forma de "alcançar" a ligação do worker 2 diretamente — precisas de um mecanismo de **pub/sub** partilhado entre processos. Redis é a escolha mais comum:

```python
import redis.asyncio as redis
import asyncio
from fastapi import FastAPI, WebSocket

app = FastAPI()
redis_client = redis.Redis(host="localhost", port=6379)

@app.websocket("/ws/chat/{room_id}")
async def chat_endpoint(websocket: WebSocket, room_id: str):
    await websocket.accept()
    pubsub = redis_client.pubsub()
    await pubsub.subscribe(f"room:{room_id}")

    async def reader():
        async for message in pubsub.listen():
            if message["type"] == "message":
                await websocket.send_text(message["data"].decode())

    reader_task = asyncio.create_task(reader())
    try:
        while True:
            data = await websocket.receive_text()
            # publica para TODOS os workers subscritos a esta sala
            await redis_client.publish(f"room:{room_id}", data)
    finally:
        reader_task.cancel()
        await pubsub.unsubscribe(f"room:{room_id}")
```

Cada worker que tem clientes ligados a uma sala subscreve o canal Redis correspondente. Quando qualquer worker recebe uma mensagem de um cliente, publica-a no Redis; todos os workers subscritos (incluindo os que têm os destinatários ligados) recebem-na e reenviam-na pelos seus próprios sockets. É o mesmo padrão que usarias com Django Channels (que usa exatamente Redis como "channel layer" por baixo).

## Segurança e autenticação

Alguns pontos que costumam ser negligenciados:

- **`wss://` em produção**, sempre — é o equivalente a `https://` para WebSockets, com TLS. Sem isto, qualquer intermediário na rede lê e altera as mensagens em claro.
- **Autenticação no handshake**, não depois: como o WebSocket não tem um conceito nativo de "headers por mensagem" como o HTTP, a autenticação normalmente é feita com um token JWT passado como query string (`wss://exemplo.com/ws?token=...`) ou via cookie de sessão já existente, validado em `websocket.accept()` — nunca confies em mensagens de autenticação enviadas *depois* de aceitar a ligação, porque nesse intervalo já aceitaste tráfego de alguém não autenticado.
- **Validação de `Origin`**: por defeito, browsers não aplicam CORS a WebSockets da mesma forma que a `fetch()`. Se não validares o header `Origin` no handshake, qualquer site pode abrir um WebSocket para o teu servidor a partir do browser de um utilizador autenticado (um padrão análogo a CSRF).
- **Rate limiting por ligação**: como a ligação fica aberta, um cliente malicioso pode enviar mensagens a um ritmo muito mais alto do que conseguiria com pedidos HTTP individuais — vale a pena limitar mensagens/segundo por ligação.

## Quando (não) usar

WebSockets valem a complexidade extra quando precisas de baixa latência **nos dois sentidos** e com frequência — chat, colaboração em tempo real, jogos, trading. Se só precisas que o servidor empurre atualizações ocasionais para o cliente (notificações, progresso de um job em background), Server-Sent Events costuma ser mais simples de implementar, operar e escalar, precisamente por correr sobre HTTP normal e não precisar de gestão de estado distribuído como o exemplo do Redis acima.
