# System_Design_Overvieew

# Concepts

1 - [CAP Theorem](#cap-theorem-section)

2 - [OSI Model](#osi-model)

3 - [TCP/IP Model](#tcp/ip-model)

4 - [ACID and SQL/NoSQL](#acid-sql-nosql)

5 - [Communication in Real-Time - WebSockets](#websockets)

6 - [Partitioning, Sharding & Consistent Hashing](#sharding&partitioning)

7 - [Caching](#caching)

8 - [Replication & Consistency Models](#replication)

9 - [SOLID, KISS , ACID...](#solid,kiss)


# Practical cases or blog posts

- [Building Real time feed](#oddfeed)



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



<a name="oddfeed"><a/>
# Building a real-time odds feed

Imagine you're asked to build the following product. Your company collects betting odds from more than 300 bookmakers and betting exchanges around the world. Your customers are hedge funds, market makers, and sportsbooks — people who trade on these prices. When Real Madrid scores in the 87th minute, the odds on every bookmaker in the world start moving within seconds, and your customers need to see those movements *as they happen*. Not in a minute. Not in ten seconds. In milliseconds, because whoever sees the price move first gets to act on it first.

That's the whole product: data goes in from 300 places, data goes out to hundreds of clients, and the clock is always ticking.

It sounds simple when you say it in one sentence. It stops being simple the moment you start sketching it. This post walks through the problem the way you'd walk through it in a system design discussion: first understanding why the obvious approaches fail, then introducing the concepts you need one at a time, and finally assembling them into an architecture where every component earns its place.

## Why the obvious approach doesn't work

Let's start with the design every developer sketches first, because understanding why it breaks teaches you everything else.

The naive version: build a REST API. Store the latest odds in a database. Clients call `GET /odds/real-madrid-vs-barcelona` whenever they want fresh prices.

The first problem is a question: *how often do clients call it?* If a hedge fund polls every 5 seconds, they're up to 5 seconds behind the market — an eternity in trading. So they poll every 100 milliseconds instead. Now multiply: 500 clients × 10 requests per second × dozens of markets each. You're serving hundreds of thousands of requests per second, and the punchline is that **most of those requests return nothing new**. Odds on a quiet market might not change for minutes. You're burning enormous resources answering the question "did anything change?" with "no."

Polling has a fundamental shape problem: the client is asking, but only the server *knows* when something changed. The communication is pointed the wrong way. What you actually want is for the server to stay quiet until something happens, and then *push* the update to everyone who cares, immediately.

This is the first concept we need.

## Concept 1: WebSockets, or how a server pushes

Normal HTTP is request/response: the client asks, the server answers, the connection is done. The server has no way to spontaneously say something to a client — it can only ever *answer*.

A **WebSocket** flips this. It starts life as a normal HTTP request with a special header that says "let's upgrade this connection." The server agrees, and from that moment the underlying TCP connection stays open — for minutes, hours, days. Both sides can now send messages to each other at any time, in either direction, with no new connection setup and no asking.

For our odds feed, this changes the shape of everything. A client connects once, says "I'm interested in La Liga football and NBA basketball," and then just *listens*. When a price moves, the server pushes one small message down that already-open pipe. No polling, no wasted requests, no "did anything change?" — the client hears about changes precisely when they happen and never otherwise.

<img width="1360" height="600" alt="image" src="https://github.com/user-attachments/assets/2b85f8b7-3769-4964-9fbb-ee55aa61aefc" />


WebSockets also give us a channel back: the client can send "subscribe me to this new match" or "unsubscribe from tennis" over the same connection, and the server adjusts what it sends. This subscription model matters — no client wants all 300 bookmakers × 12,000 tournaments. They want their slice.

So: WebSockets solve the last mile, the hop between our system and the client. But we haven't built the system yet. Where do the odds come from, and what happens to them before they reach that WebSocket?

## The input side: 300 sources, 300 headaches

Here's an assumption worth making explicit: **the 300 bookmaker feeds are not under our control, and they are all different.** Some bookmakers offer their own WebSocket streams. Some offer REST APIs we have to poll. Some offer nothing, and the data has to be scraped from their websites. They use different formats (JSON here, XML there, a binary protocol somewhere else), different update frequencies, and — this is the sneaky one — **different names for the same things**.

One bookmaker lists a match as "Man Utd v Man City." Another says "Manchester United — Manchester City." A third uses an internal ID like `evt_884213`. They are all the same fixture, but nothing in the data says so. The same applies to markets ("Over/Under 2.5" vs "Total Goals 2.5") and outcomes.

This gives us the first two components of our system.

**Connector adapters.** One small program per source (or per source *type*). The adapter that talks to Bookmaker A's WebSocket knows how to keep that connection alive and reconnect when it drops. The adapter for Bookmaker B knows how to poll their REST API politely. The adapter for Bookmaker C drives a headless browser to scrape prices off a page. Each adapter has exactly one job: get raw data out of one source and hand it inward. The key design idea is **isolation** — when Bookmaker C's website changes its HTML at 2am and the scraper breaks, only that one adapter fails. The other 299 sources keep flowing. Adapters are the crumple zone of the system: they absorb the chaos of the outside world so nothing else has to.

**The normalization service.** Raw events from adapters flow into a service whose job is translation. It converts every incoming update into one canonical internal format — something like `{market_id, bookmaker, outcome, price, timestamp}` — and, crucially, it performs **entity resolution**: mapping each bookmaker's naming for a fixture, market, or outcome onto our own internal ID. To do this it consults a **reference data store** (a plain relational database like PostgreSQL is a good fit) holding the mapping tables: "Bookmaker A's `evt_884213` = our `fixture_29871`."

Entity resolution sounds mundane and is quietly the hardest ongoing problem in this whole system, because it's never finished — new tournaments, new naming quirks, new bookmakers arrive constantly. It's partly a code problem and partly a tooling-and-humans problem. But once an event exits normalization, everything downstream can rely on one clean, consistent shape. That contract is the payoff.

At this point we have clean events being produced on one side, and WebSocket connections waiting on the other side. The tempting move is to connect them directly: normalization finishes an event, loops over the WebSocket servers, and sends it to each. Let's see why that's a trap.

## Why we don't wire the middle directly

Think about what direct calls mean. The normalization service must maintain a list of every WebSocket server, so every time we add or remove a server, normalization has to know. If one WebSocket server is slow or restarting mid-deploy, normalization either waits on it (slowing everyone down) or skips it (losing data for that server's clients). If a WebSocket server crashes and comes back thirty seconds later, everything published during those thirty seconds is simply gone — nobody kept it. And when the risk team later asks "can our new analytics service also receive this stream?", you have to modify normalization *again* to add another destination.

Every one of these problems comes from the same root: **the producer of data knows too much about its consumers.** They're coupled. What we want is a component in the middle whose entire purpose is to *be* the middle — so producers and consumers never have to know about each other at all.

## Concept 2: the event bus

An **event bus** (also called a message broker or event streaming platform) is infrastructure with one deceptively simple job. Producers publish events *to the bus*. Consumers read events *from the bus*. Neither side knows the other exists.

The normalization service finishes processing an update and publishes one message — "odds for market X are now 2.35, timestamp T" — to a named channel on the bus (channels are usually called **topics**; ours might be `odds.updates`). Then it moves on to the next event. It doesn't know how many consumers exist, whether they're keeping up, or whether one of them is currently mid-restart. Not its problem anymore.

On the other side, any number of consumers subscribe to the topic and read the stream of events, each at their own pace. Add a new consumer next year? It just subscribes. Nothing upstream changes.

The most widely used tool in this category is **Apache Kafka**, and it adds three properties that turn "convenient middleman" into "backbone of the architecture." They're worth understanding individually.

**First: the bus is a log, not a mailbox.** Kafka doesn't hand a message to a consumer and forget it. It *appends* every event to a durable, ordered log on disk and keeps it for a configurable period. Consumers don't "receive" messages so much as *read* the log, and each consumer tracks its own position in it — a bookmark called an **offset**. This has a beautiful consequence: if a consumer crashes and restarts, it simply resumes reading from its last bookmark. Those thirty seconds of downtime? The events are all still in the log, waiting. Nothing was lost, and nobody had to resend anything.

<img width="1360" height="640" alt="image" src="https://github.com/user-attachments/assets/ed3e3287-8331-456e-aca3-9f7d65321937" />


**Second: partitions give you ordering and scale.** A busy topic is too much for one machine, so Kafka splits a topic into **partitions** — independent sub-logs that can live on different servers. When publishing, you choose a partition using a key, and here's where a design decision quietly solves a critical requirement: we use `market_id` as the key. That means *all updates for the same market always land in the same partition*, and within a partition, order is strictly preserved. Why does that matter so much? Because the one thing a trading client can never tolerate is seeing prices go *backwards* — receiving a stale price after a fresher one and believing the stale one is current. Partitioning by market ID means the sequence "2.30, then 2.35, then 2.40" for a given market can never arrive shuffled. We get per-market ordering as a structural guarantee, not as something we have to check. (Notice we did *not* ask for global ordering across all markets — we don't need it, and demanding it would destroy our ability to scale. Knowing which ordering guarantee you actually need is one of those distinctions that separates a good design from a hand-wavy one.)

**Third: consumer groups let many teams share one stream.** A **consumer group** is a named set of consumer processes that cooperate to read a topic — Kafka divides the partitions among the group's members automatically. The crucial part: *different* groups are completely independent. Each group has its own offsets and reads the full stream at its own pace. So our WebSocket servers form one group (`ws-edge`), a risk-analytics service forms another (`risk-mgmt`), and tomorrow's machine-learning pipeline forms a third — all consuming the same events, none affecting the others. One slow consumer group lags *itself*; it cannot slow down anyone else. The single stream of truth gets reused endlessly, for free.

One honest caveat, because good design discussions include them: Kafka is not weightless. It's a distributed system you now have to operate, and it adds a few milliseconds of latency versus lighter alternatives like Redis pub/sub (which is blazingly fast but keeps nothing — miss a message and it's gone forever). For a product whose clients trade real money on this data, durability and replay win the argument. But it's an argument, not a reflex.

## The output side: fan-out and its problems

Now the layer that holds the WebSockets: a fleet of **gateway servers**. Each one does the following, continuously: hold open connections to some fraction of our clients (say, a few thousand each), remember what each client subscribed to, consume the event stream from Kafka, and for every event ask "which of *my* clients care about this market and are entitled to see this bookmaker?" — then push it to exactly those connections.

This layer has three problems worth understanding, because they're where real-time systems actually get hard.

**Problem one: connections are sticky, so make the servers interchangeable.** An HTTP request lasts milliseconds and any server can handle it. A WebSocket connection lasts hours and lives on *one specific server* — you can't round-robin it away mid-conversation. The way out is to make the gateway servers **stateless** in every way that matters: no gateway holds data that only it has. All the odds flow through Kafka, which every gateway reads. Client subscriptions can be re-sent by the client on connect. So when a gateway dies, its clients reconnect (through a load balancer) to any other gateway and lose nothing, because the replacement gateway has access to exactly the same stream and can rebuild the same view. The connection is sticky; the *server* is disposable.

**Problem two: slow clients, and the art of dropping messages correctly.** During a busy Saturday of in-play football, updates arrive fast. What if one client's network is struggling and they can only consume half as fast as we're sending? If we buffer everything for them, memory grows without bound and eventually the server dies — taking thousands of *healthy* clients with it. If we drop random messages, the client might miss a price and trade on stale data. The elegant answer is **conflation**: per client, per market, keep only the *latest* price. If a slow client misses three intermediate ticks — 2.30, 2.32, 2.35 — while their connection was congested, we skip straight to 2.35 when they can receive again. This works because of something we identified back in the requirements: clients don't need *every* message, they need the *current truth*. Intermediate prices that were already superseded have no value. Conflation drops exactly the messages that don't matter and never the one that does. Getting delivery semantics right isn't about never dropping anything — it's about knowing precisely what you're allowed to drop.

<img width="1360" height="540" alt="image" src="https://github.com/user-attachments/assets/a04f8988-7ebc-4ac8-ae28-288a54c15621" />


**Problem three: reconnection needs a source of truth.** A WebSocket stream carries *changes* — deltas. But a client that just connected (or reconnected after a network blip) has no state to apply deltas *to*. If they just start listening, they know about the markets that happen to tick in the next minute and nothing else. So we add a **snapshot store**: a small service (another Kafka consumer group!) that maintains "current price for every market" in a fast key-value store like Redis, each entry stamped with a sequence number. The client protocol becomes: on connect, receive the full snapshot of your subscribed markets first, then apply the live deltas — using the sequence numbers to discard any delta older than the snapshot you got. This snapshot-then-delta pattern appears in virtually every real-time market data system ever built, because the underlying problem — streams tell you what changed, not what *is* — is universal.

<img width="1360" height="720" alt="image" src="https://github.com/user-attachments/assets/b06d004d-3036-4e0d-be76-660e73abe09f" />


Two smaller components round out the picture. A **load balancer** in front of the gateways — operating at the TCP level rather than the HTTP level, because we're balancing long-lived connections, not individual requests — spreads clients across the fleet. And an **auth service** validates each client at the WebSocket handshake (typically via a signed token like a JWT) and loads their *entitlements*: which sports, which bookmakers, which markets they've paid for. Entitlements come with a subtle wrinkle: a connection authorized at 9am might have its contract change at noon, so a long-lived connection needs a way to be re-checked or terminated mid-flight — authorization can't be a one-time event at the front door.

## The whole picture

Trace one price change through the finished system. A bookmaker moves a price. Their adapter catches it within milliseconds and forwards the raw event. Normalization translates it into the canonical schema, resolving the bookmaker's fixture naming against the reference store. The clean event is published to Kafka, landing in the partition owned by its `market_id`, behind every previous update for that market and in front of every future one. Three consumer groups pick it up independently: the snapshot service overwrites its Redis entry for that market, the risk pipeline updates its models, and every gateway server checks the event against its connected clients' subscriptions and pushes a small frame down each matching WebSocket. Total time, source to screen: tens of milliseconds.

<img width="1360" height="1400" alt="image" src="https://github.com/user-attachments/assets/cf07e5d8-cf59-4b87-bebb-d14af221acc7" />


Now notice what the architecture bought us — the part that's easy to miss when you only see the final diagram. Every component can **fail independently without cascading**. A scraper breaks: one source goes stale, 299 keep flowing. A gateway crashes: its clients reconnect elsewhere, pull a snapshot, resume — the log kept everything in the meantime. A consumer group falls behind: it lags itself, and a metric called **consumer lag** (how far behind the log's head a group is reading) tells your on-call engineer exactly where the pain is. And every layer **scales along its own axis**: more bookmakers means more adapters, more clients means more gateways, more throughput means more partitions — each independently, none requiring changes to the others.

That independence isn't decoration. It's the entire reason the system is shaped this way. Every box in the diagram exists because gluing its responsibilities onto a neighbor would couple two things that need to fail, scale, or evolve separately. The bus isn't there because Kafka is fashionable; it's there because the alternative is a producer that must know its consumers. The snapshot store isn't there because Redis is fast; it's there because streams carry change, and change is meaningless without a starting point.

Which is, in the end, the transferable lesson. Strip away the sports betting and the same skeleton underlies stock tickers, multiplayer game state, collaborative editors, live dashboards, chat systems: **many unreliable sources, one normalized truth, a durable ordered log in the middle, stateless push servers at the edge, and snapshots for anyone who just walked in.** Learn the shape once, and you'll recognize it everywhere real-time data moves.

### aws possible setup

<img width="2000" height="1160" alt="image" src="https://github.com/user-attachments/assets/19b1d787-044f-43fc-8a0a-c8312c865fdb" />

How it maps to concrete AWS services, reading the two paths:

**Ingest path (top).** Bookmaker feeds arrive from the public internet through a NAT Gateway into the VPC. Connector adapters run as ECS tasks — one service per source type, so a broken scraper only restarts its own task. Normalization runs as another ECS service, reading fixture/market ID mappings from **RDS PostgreSQL** (the reference data store), and publishes canonical events to **Amazon MSK** — managed Kafka, which is where the `odds.updates` topic and its `market_id` partitioning live.

**Serving path (bottom).** Clients resolve through **Route 53** to a **Network Load Balancer** — L4, not an ALB, because long-lived WebSocket connections are what's being balanced, not requests. The WebSocket gateways are ECS tasks consuming MSK as one consumer group and pushing to their connected clients. The bidirectional arrow to **ElastiCache (Redis)** is the snapshot store: gateways read current state from it on client connect, and a separate consumer writes it.

**Down the right side**, the cross-cutting services: IAM/Cognito for auth and per-client entitlements, CloudWatch (where **consumer lag** is the single most important alarm — it tells you a consumer group is falling behind before clients notice stale prices), and CloudTrail for audit.

## NAT Gateway

**Role: lets things in a private subnet make *outbound* connections to the internet, without being reachable *from* the internet.**

Our connector adapters live in a private subnet — they have no public IP, so nothing on the internet can dial into them. That's what we want for security. But they still need to reach *out* to 300 bookmaker websites and APIs. The NAT Gateway is what makes that possible: it sits in a public subnet, and outbound traffic from the private subnet gets routed through it, appearing to the outside world as coming from the NAT's public IP. Return traffic (the bookmaker's response, or the frames of a WebSocket the adapter opened) flows back through it.

**And here's the correction:** the arrow in my diagram points *from* the bookmaker feeds *into* the NAT Gateway, which is misleading. NAT is fundamentally asymmetric — connections must be **initiated from inside**. Bookmakers never connect to us; our adapters connect to them and the odds flow back over connections we opened. So the arrow should really point outward from the adapters, with data returning along it. The dataflow direction (odds coming toward us) and the connection direction (us reaching out) are opposite, and the diagram conflated them.

## Route 53

**Role: DNS. It turns a hostname into an address.**

Clients don't connect to an IP; they connect to something like `wss://feed.55tech.com`. Route 53 is AWS's DNS service, and it answers that lookup with the address of our Network Load Balancer. That's the *whole* job in the basic case.

But it's on the diagram for a second reason: Route 53 can do **health-checked, latency-based, or failover routing**. If we run the serving stack in two regions, Route 53 can send a London client to the London load balancer and a New York client to the US one (latency routing), and if one region goes dark, health checks pull it out of DNS so new connections go to the survivor. For a latency-sensitive product where clients are globally distributed, that's meaningful — DNS becomes the first layer of traffic steering, not just name lookup.

## Amazon MSK

**Role: it's just Kafka, run by AWS instead of by you.**

MSK = "Managed Streaming for Apache Kafka." Everything we discussed about the event bus — the durable ordered log, partitions keyed by `market_id`, offsets, consumer groups — is Kafka semantics, unchanged. MSK doesn't add features; it removes operational work. AWS runs the brokers, handles replication across availability zones, patches them, replaces failed nodes.

The reason it shows up as its own box: Kafka is genuinely painful to self-host well (broker sizing, ZooKeeper/KRaft, rebalancing, disk management). MSK is what you pick when you want Kafka's guarantees without hiring someone to babysit a Kafka cluster. In an interview, saying "Kafka, deployed as MSK" signals you know the difference between the *protocol/semantics* and the *operational burden*.

## CloudWatch

**Role: metrics, logs, and alarms — the system's nervous system.**

Everything emits into it: ECS task CPU/memory, NLB connection counts, MSK broker health, application logs from the gateways. You then define alarms on top ("page someone if X crosses Y").

The one metric worth singling out, and the reason I labeled it that way on the diagram: **consumer lag**. Remember that each consumer group has its own offset — a bookmark into the log. Consumer lag is the distance between a group's bookmark and the head of the log: "how many events behind is this consumer right now?" It's the single best early-warning signal in this whole architecture, because if the WebSocket gateway group starts lagging, it means clients are receiving *stale prices* — and lag climbs *before* anything actually breaks. Nothing errors, nothing crashes; the data just quietly gets old. Without a lag alarm you'd find out from an angry customer instead of from a page.

## ECS — what it actually is

**ECS is not a machine. It's an orchestrator: software that decides which containers run where, keeps them alive, and scales them.**

You give it a **task definition** — essentially "here's a Docker image, give it 1 vCPU and 2GB, these env vars." ECS then runs **tasks** (a running instance of that definition, i.e. a running container) and groups them into a **service**, which is the thing that says "always keep 6 of these running; if one dies, start a replacement; if load rises, add more."

To your question — yes, there are many containers, and the diagram is showing you *services*, not machines. That single "ECS — WebSocket gateways" circle is really *N* identical gateway containers running in parallel, each holding a few thousand client connections. Scaling that tier means telling ECS to run more tasks of that definition. Same for connectors and normalization: three separate ECS services, each with its own count and its own scaling rules. That's the payoff of the stateless design — "add capacity" is just a number change.

Where the containers physically run depends on the **launch type**:
- **Fargate** — you don't manage servers at all. AWS finds the compute; you just declare CPU/memory per task. Simpler, slightly pricier per unit.
- **EC2 launch type** — you run a cluster of EC2 VMs yourself and ECS packs containers onto them. More control (instance types, networking tuning), more work.

(This is why the reference diagram shared showed ECS with EC2 instances beside an Auto Scaling Group — that's the EC2 launch type: ASG scales the *VMs*, ECS packs *containers* onto them. Two levels of scaling. With Fargate, the EC2/ASG layer just disappears.)

<img width="2000" height="1160" alt="image" src="https://github.com/user-attachments/assets/c1af22d5-fe06-480f-aab0-39c6fbc1b81f" />


<a name="sharding&partitioning"><a/>
# 6 - Partitioning, Sharding & Consistent Hashing

# Partitioning, Sharding & Consistent Hashing

*Everything you need to hold your own on this topic in a system design interview.*

---

## 1. The problem: one machine is not enough

You have 50 TB of user data and 500k writes per second. No single Postgres box takes that. You have three levers:

| Lever | What it fixes | What it doesn't |
|---|---|---|
| **Vertical scaling** | Buys you time | Hard ceiling, expensive, single point of failure |
| **Replication** | Read throughput, availability | Every replica still stores *all* the data; writes still go to one leader |
| **Partitioning (sharding)** | Storage *and* write throughput | Adds a routing problem, cross-shard queries, rebalancing |

**Partitioning** = splitting one logical dataset into N subsets, each owned by a different node. Every key belongs to exactly one partition. Partitioning and replication are orthogonal and almost always used together: you partition into N shards, then replicate each shard 3×.

> Terminology: "partition" (Kafka, Cassandra, DDIA), "shard" (MongoDB, Elasticsearch, Vitess), "region" (HBase), "vBucket" (Couchbase). Same idea. In interviews, use whichever the interviewer used.

Also distinguish:
- **Horizontal partitioning** — split by *rows* (users A–M here, N–Z there). This is sharding. This is what the rest of the post is about.
- **Vertical partitioning** — split by *columns/tables* (profile data here, billing data there). Really just service decomposition.

---

## 2. How do you decide which node owns a key?

### 2.1 Range partitioning

Sort keys and slice into contiguous ranges: `a–f → shard 0`, `g–m → shard 1`, …

- ✅ **Range scans are cheap.** "Give me all events between 09:00 and 10:00" hits one shard.
- ❌ **Hotspots.** If your key is a timestamp, *all* current writes land on the last shard. Classic anti-pattern.
- Used by: HBase, Bigtable, CockroachDB, MongoDB (ranged sharding).

Mitigation for the timestamp problem: prefix the key with something high-cardinality (`{sensor_id}:{timestamp}`), which restores write spread but means a time-range scan now hits every shard.

### 2.2 Hash partitioning

`shard = hash(key) % N`.

- ✅ **Uniform load** if the hash is decent (MD5, MurmurHash, xxHash — *not* Python's `hash()`, which is randomized per process, and not a cryptographic hash you're paying for unnecessarily).
- ❌ **Range scans are dead.** Adjacent keys land on unrelated shards.
- ❌ **And the big one: `% N` breaks catastrophically when N changes.** See below.

### 2.3 Directory / lookup-based

Keep an explicit map `key-range → shard` in a coordination service (ZooKeeper, etcd) or a lookup table. Vitess and pre-2019 Figma-style setups do this.

- ✅ Total control: you can move any partition anywhere, split hot ones arbitrarily.
- ❌ The directory is another component to keep available and consistent, and it's on the hot path (so you cache it aggressively and handle staleness).

---

## 3. Why `hash(key) % N` is a trap

This is the setup for consistent hashing, and interviewers love it. Make sure you can produce the number.

You have 4 cache nodes. `shard = hash(key) % 4`. Add a fifth node → `% 5`.

For a key to stay put, you need `hash(key) % 4 == hash(key) % 5`. That's true for roughly 1 in 5 keys. **~80% of your keys now map to a different node.**

Concretely, with 4 nodes:

```
key       hash   % 4   % 5   moved?
--------------------------------------
user:1     102     2     2     no
user:2     313     1     3     YES
user:3     577     1     2     YES
user:4     844     0     4     YES
```

Consequences:
- **Cache layer:** near-total miss rate → every request stampedes the database → the database falls over. Adding a node to relieve load *takes the system down*. This is the failure mode that made Akamai invent consistent hashing in 1997.
- **Database layer:** you must physically move ~80% of your data before the new topology is usable. On 50 TB, that's not a maintenance window, that's a project.

The general rule: with `% N`, changing N moves roughly `(N-1)/N` of the keys. **What we want is to move only ~1/N of the keys** — just the new node's fair share.

---

## 4. Consistent hashing

### The ring

1. Take a hash function with output space `[0, 2^32)`. Bend that range into a circle — 0 and 2^32−1 are adjacent.
2. Hash each **node** (by name/IP) onto a point on the ring.
3. Hash each **key** onto a point on the ring.
4. **A key is owned by the first node you meet walking clockwise from the key's position.**

```
              0 / 2^32
                 │
      NodeC ─────┼───── NodeA
          ╱      │      ╲
         ╱   ●k3 │  ●k1  ╲
        │        │        │
        │  ●k4   │   ●k2  │
         ╲       │       ╱
          ╲______│______╱
              NodeB

   k1, k2 → NodeB   (first node clockwise)
   k3     → NodeA
   k4     → NodeB
```

### Why it works

**Add a node.** NodeD lands somewhere on the ring between NodeC and NodeA. It takes over exactly the keys in the arc from NodeC to NodeD — keys that used to belong to NodeA. **Nobody else is touched.** Only ~1/N of keys move, and they move from exactly one node.

**Remove a node.** Its arc collapses into its clockwise successor. Only that node's keys move, only to one node. Everyone else is untouched.

That's the whole trick: with `% N`, node identity is *positional* (node #3 of 5), so changing the count renumbers everything. On the ring, node identity is *absolute* (a point at hash 0x8A3F…), so adding a node only perturbs its immediate neighbourhood.

### Virtual nodes (vnodes) — do not skip this

Naive consistent hashing with, say, 5 nodes on the ring has two problems:

1. **Uneven distribution.** Five random points on a circle do not produce five equal arcs. With N random points, the expected largest arc is a *lot* bigger than 1/N — expect load imbalance of 2–3× with small N. One node gets hammered.
2. **Uneven failure handling.** When a node dies, *its entire load* is dumped on a single successor — which may then die too. Cascading failure.

**Fix:** hash each physical node onto the ring many times — `node_a#0`, `node_a#1`, … `node_a#199`. Each of these is a *virtual node* pointing back to the same physical node.

With ~100–500 vnodes per physical node:
- Arcs average out; load imbalance drops to a few percent (standard deviation shrinks as ~1/√V).
- When a node dies, its hundreds of small arcs are inherited by *many different* successors → the load is spread, not dumped.
- **Heterogeneous hardware falls out for free:** give the beefy box 400 vnodes and the small one 100, and it gets 4× the keys.

This is what Cassandra means by `num_tokens` and what Dynamo calls "tokens."

### Implementation sketch

The standard implementation is a sorted array of vnode hashes plus a binary search — `O(log V)` lookups, no allocation on the hot path.

```python
import bisect
import hashlib

class HashRing:
    def __init__(self, nodes=(), vnodes=150):
        self.vnodes = vnodes
        self._ring: dict[int, str] = {}   # hash -> physical node
        self._sorted: list[int] = []      # sorted hashes, for bisect
        for n in nodes:
            self.add_node(n)

    @staticmethod
    def _hash(key: str) -> int:
        return int.from_bytes(hashlib.md5(key.encode()).digest()[:4], "big")

    def add_node(self, node: str) -> None:
        for i in range(self.vnodes):
            h = self._hash(f"{node}#{i}")
            self._ring[h] = node
            bisect.insort(self._sorted, h)

    def remove_node(self, node: str) -> None:
        for i in range(self.vnodes):
            h = self._hash(f"{node}#{i}")
            del self._ring[h]
            self._sorted.remove(h)

    def get_node(self, key: str) -> str | None:
        if not self._sorted:
            return None
        h = self._hash(key)
        idx = bisect.bisect(self._sorted, h)   # first vnode clockwise
        if idx == len(self._sorted):           # wrapped past 2^32
            idx = 0
        return self._ring[self._sorted[idx]]
```

Ten lines of real logic. Being able to write this on a whiteboard is a very reasonable ask in a mid/senior interview.

### Replication on the ring

Replication factor 3? Walk clockwise from the key and take the **first 3 distinct physical nodes**. (Distinct is the important word — otherwise your three "replicas" can be three vnodes of the same box, and you've replicated nothing.) Production systems go further and skip successors in the same rack or availability zone.

This gives you a *preference list* per key, which is exactly what Dynamo/Cassandra use for quorum reads and writes (`R + W > N`).

---

## 5. Consistent hashing isn't the only answer (and often isn't the one used)

Interviewers like this because it shows you're not just reciting the blog-post canon.

**Fixed partitions (a.k.a. hash slots).** Pick a large, *fixed* number of partitions up front — far more than you'll ever have nodes — and assign partitions to nodes via a small map. Redis Cluster uses **16384 hash slots** (`CRC16(key) % 16384`); Elasticsearch fixes shard count at index creation; Kafka fixes partitions per topic. Adding a node just means moving *whole partitions* to it — cheap, explicit, and easy to reason about. The catch: you can't easily change the partition count later, so you must over-provision (this is why resharding Elasticsearch is painful and why Kafka partition counts are a permanent decision).

**Rendezvous hashing (HRW).** For each key, compute `hash(key + node)` for every node and pick the max. Same "only 1/N keys move" property, no ring, no vnodes, trivially supports weights — but lookup is `O(N)` instead of `O(log V)`. Great when N is small (load balancers, caches).

**Jump consistent hash** (Google). ~5 lines, no memory, perfectly balanced, `O(log N)` — but it only maps to bucket *indices* `0..N-1` and can only add/remove buckets at the *end*, so it can't express "node 3 died."

**Maglev hashing** (Google's L4 load balancer). Builds a big lookup table; optimizes for near-perfect balance and fast lookups, tolerating slightly more disruption than the ring.

Good interview line: *"Consistent hashing is the right answer for a large, dynamic, homogeneous fleet with no central coordinator — caches, Dynamo-style stores. If I have a coordinator anyway, fixed partitions with a slot map are simpler to operate and let me move load deliberately."*

---

## 6. The things that actually go wrong

These follow-ups are where interviews are won or lost.

**Hot keys / the celebrity problem.** Consistent hashing balances *keys*, not *traffic*. If one key gets 40% of the reads (Taylor Swift's follower list, a viral post ID), no amount of hashing helps — it's one key, it lives on one node.
Mitigations: append a random suffix to split the key across shards (`celeb:123:{0..9}`) and fan-out reads; put a dedicated cache in front of the hot key; give hot tenants their own dedicated shard.

**Skewed shards.** Same idea at the tenant level: one B2B customer is 100× everyone else. Detect it, then split it out by hand — this is where a directory-based scheme earns its keep.

**Rebalancing during a move.** While partition P is migrating from node A to node B, who serves reads? Standard answer: keep A authoritative, stream the data to B in the background, then flip the routing atomically and let A forward stragglers for a while. Never rebalance *automatically* on a node timeout — a node that's slow for 30 seconds isn't dead, and auto-rebalancing turns a blip into a data-shuffling storm.

**Cross-shard reads.** A query that isn't scoped by the partition key must be scattered to every shard and gathered — latency becomes p99 of N nodes, which is bad and gets worse with N. This is why **the partition key choice is the single most important design decision**: pick the key that most of your read path already filters by.

**Secondary indexes.** Two options: *local* (each shard indexes its own data → writes are cheap, reads must scatter-gather; Cassandra, Elasticsearch) or *global* (the index itself is partitioned by the indexed term → reads hit one shard, but writes now touch two shards and need distributed-transaction handling; DynamoDB GSIs, which is why they're eventually consistent).

**Cross-shard transactions and joins.** Basically: don't. Denormalize, or co-locate related data by giving them the same partition key (Cassandra's partition key vs. clustering key; DynamoDB's PK vs. SK), or accept two-phase commit and the availability hit.

**Routing: who does the lookup?**
1. Client-side (client library holds the ring — Memcached clients, Cassandra drivers). Fastest, but every client needs topology.
2. Proxy/router tier (Vitess, Twemproxy, mongos). Clean, one extra hop.
3. Any-node redirect (Redis Cluster's `MOVED` responses). Node tells the client where to go.

---

## 7. Real systems (name-drop these correctly)

| System | Scheme |
|---|---|
| **Amazon Dynamo / Cassandra / Riak** | Consistent hashing ring + vnodes, replication to the next N distinct nodes |
| **Redis Cluster** | 16384 fixed hash slots, `CRC16(key) % 16384`, slots assigned to nodes |
| **Memcached** | Consistent hashing, done entirely in the *client* library |
| **Kafka** | Fixed partitions per topic; default `murmur2(key) % num_partitions` |
| **DynamoDB** | Hash of partition key → internal partitions; splits hot/large partitions automatically |
| **Elasticsearch** | `murmur3(_routing) % num_primary_shards`, fixed at index creation |
| **MongoDB** | Ranged or hashed sharding + a config-server directory, chunk-based balancer |
| **Vitess (YouTube)** | Directory/keyspace-based, explicit resharding |

Note the pattern: **stateless-ish caches and leaderless stores use the ring; systems that already have a control plane use fixed partitions + a map.**

---

## 8. The interview checklist

If sharding comes up, work through these out loud, in this order:

1. **Why shard at all?** State the bottleneck — storage, write throughput, or blast radius. Don't shard because it's fun; interviewers will ask if a bigger box or a read replica would do.
2. **What's the partition key?** Justify it from the read path. Say what it makes cheap and what it makes expensive.
3. **Range or hash?** Trade range-scan support against hotspot risk.
4. **How do you add a node?** This is your cue: explain `% N` moving ~80% of keys, then introduce the ring.
5. **How do you keep it balanced?** Virtual nodes. Explain the two reasons (distribution + smooth failure spread), not just the first.
6. **How do you replicate?** Next N *distinct* physical nodes clockwise; rack/AZ awareness.
7. **What about hot keys?** Show you know hashing doesn't solve this. Key-splitting, dedicated cache, dedicated shard.
8. **How does a client find its data?** Client-side ring / router tier / redirect.
9. **What breaks?** Cross-shard queries, global secondary indexes, transactions, rebalancing storms.

### Quickfire answers

**"How many keys move when I add the Nth node?"** ~1/N of them, from one node (naive ring) or spread across many (with vnodes). Compare with ~(N−1)/N for modulo.

**"Why not just use `hash % N`?"** Because N changes, and when it does, almost everything moves. Fine only if N is truly fixed forever.

**"Why virtual nodes?"** Even distribution with few physical nodes; spread the failed node's load across many successors instead of one; weight heterogeneous hardware.

**"What's the lookup cost?"** Binary search over a sorted array of V·N vnode hashes: `O(log(V·N))`, in-memory, no network.

**"Does consistent hashing balance load?"** It balances *keys*. Load balance additionally requires that key access is roughly uniform — and it usually isn't.

---

## 9. Further reading

- **Karger et al., 1997** — the original consistent hashing paper (written for web caching, not databases).
- **Dynamo: Amazon's Highly Available Key-value Store (2007)** — §4.2 is the canonical ring + vnodes + preference list description.
- **DDIA, Chapter 6** — "Partitioning." Kleppmann's framing of range vs. hash vs. fixed-partitions is exactly what senior interviewers expect back from you.
- **Jump Consistent Hash (Lamping & Veach, 2014)** and **Maglev (2016)** — for showing you know the ring isn't the end of the story.



<a name="caching"><a/>
# 7 - Caching

*Everything you need to hold your own on this topic in a system design interview.*

---

## 1. What caching actually buys you

A cache is a small, fast store holding a copy of data whose home is somewhere slower. That's it. But interviewers want you to be precise about *which* problem you're solving, because the answer changes the design:

| Goal | What you're really doing |
|---|---|
| **Latency** | Serving from RAM (~100 ns local, ~0.5 ms over the network) instead of disk (~100 µs SSD) or a remote DB (~1–10 ms) |
| **Throughput / load shedding** | Absorbing reads so the database doesn't have to. A 95% hit rate means the DB sees 5% of traffic — a 20× reduction |
| **Cost** | Fewer DB replicas, smaller instances |

Note the second one is usually the real reason at scale. **A cache is often a load-bearing structural component, not an optimization.** That distinction matters: if your database *cannot* survive a cold cache, the cache is part of your availability story, and losing it is an outage. Say this out loud in an interview — it's the difference between "I added Redis" and "I understand what I did."

**The precondition for caching to work at all:** the read/write ratio is skewed toward reads, *and* access is skewed toward a subset of keys (Zipfian, usually). If every key is read exactly once, a cache is pure overhead.

### The one law

> **A cache is a copy. Every copy can be stale. Everything hard about caching follows from this sentence.**

---

## 2. Where caches live

Work outward from the client — interviewers like candidates who name the whole stack rather than jumping straight to Redis:

1. **Client / browser cache** — HTTP `Cache-Control`, `ETag`, `Last-Modified`. Free, zero network hops, and completely uncontrollable once you've shipped it (you cannot invalidate a browser cache; you can only set short TTLs or change the URL — which is why bundlers emit `app.9f3a2b.js`).
2. **CDN / edge** (CloudFront, Cloudflare, Fastly) — caches static assets and increasingly API responses near the user. Kills latency by *geography*, not just by speed.
3. **Reverse proxy** (Nginx, Varnish) — in front of your app.
4. **Application-local / in-process cache** — a dict, an LRU, Caffeine/Guava in JVM land. Nanosecond access, but *per process*: with 6 gateway containers you have 6 independent caches that will disagree with each other. Great for tiny, rarely-changing reference data; dangerous for anything else.
5. **Distributed cache** (Redis, Memcached) — a shared tier every app instance talks to. One network hop, one shared truth. This is what people mean by "the cache."
6. **Database-internal caches** — Postgres `shared_buffers`, the OS page cache, the query plan cache. Already there, and often the reason a "slow query" is fast the second time you run it.

**Redis vs Memcached**, since it gets asked: Memcached is a pure LRU key-value blob store, multithreaded, dead simple. Redis has data structures (sorted sets, hashes, streams), persistence (RDB/AOF), replication, Lua scripting, pub/sub, and Cluster mode. Pick Memcached if you truly need a dumb cache and nothing else; in practice most teams pick Redis because they eventually want one of those extras.

**Multi-level caching** is normal (L1 in-process, L2 Redis, L3 DB). The gotcha: your L1 makes invalidation *distributed* — you now need a pub/sub channel to tell every process to drop its local copy.

---

## 3. Caching patterns (know all four, and their failure modes)

### 3.1 Cache-aside (lazy loading) — the default

The **application** owns the logic. The cache is dumb.

```python
def get_user(user_id: int) -> dict | None:
    key = f"user:{user_id}"

    cached = redis.get(key)
    if cached is not None:
        return json.loads(cached)          # hit

    user = db.fetch_user(user_id)          # miss → go to source of truth
    if user is not None:
        redis.setex(key, 300, json.dumps(user))   # populate, TTL 5 min
    return user
```

- ✅ Only requested data is cached. Cache failure is survivable — you just hit the DB.
- ❌ Every miss pays DB latency. First request after a deploy/eviction is always slow. Stale data lives until TTL expires.
- ❌ **Two callers can race**, both miss, both hit the DB, both write. See §5 (stampede).

This is what ~90% of systems do, and it's the right default answer.

### 3.2 Read-through

Same behaviour, but the *cache library or service* does the DB fetch on a miss, not your application code. The app just calls `cache.get(key)` and a loader function fires underneath. Cleaner code and a natural place to put stampede protection (many libraries coalesce concurrent loads for the same key automatically). Functionally identical from the outside.

### 3.3 Write-through

Write to the cache **and** the DB synchronously, on every write.

- ✅ Cache is never stale.
- ❌ Every write pays both latencies. And you're caching data nobody may ever read — wasted memory unless your write set is your read set.
- Usually paired with cache-aside reads.

### 3.4 Write-behind (write-back)

Write to the cache, acknowledge immediately, flush to the DB asynchronously (batched).

- ✅ Fastest writes, absorbs bursts, batches DB writes.
- ❌ **You can lose acknowledged writes if the cache dies before the flush.** The cache became your source of truth without you meaning it to.
- Appropriate for high-volume, loss-tolerant data (view counters, metrics, "last seen" timestamps). Never for money.

### The fifth pattern: write-invalidate

Not always listed, but it's what most cache-aside systems actually do on update:

```python
def update_user(user_id: int, data: dict) -> None:
    db.update_user(user_id, data)      # write the source of truth first
    redis.delete(f"user:{user_id}")    # then invalidate
```

**Delete, don't update.** Writing the new value into the cache re-introduces a race (two concurrent updates can write their values in the wrong order, leaving the cache permanently wrong). A delete is idempotent — the next reader repopulates from the DB.

**Order matters, and this is a great follow-up to be ready for.** DB-then-delete leaves a window where a concurrent reader can read the old DB value and write it back to the cache *after* your delete — the classic cache-aside race. Mitigations: short TTL as a backstop (accept it, and bound the damage), **delayed double-delete** (delete again a second later), or drive invalidation off the DB's change log (CDC/Debezium reading the WAL), which is the only genuinely correct answer and the one to mention if pushed.

---

## 4. Eviction and expiry (two different things)

**Expiry (TTL)** = *you* decided this entry is only valid for N seconds.
**Eviction** = the cache is *full* and must throw something out.

Redis `maxmemory-policy`:

| Policy | Meaning |
|---|---|
| `noeviction` | Reject writes when full. Safe for a Redis used as a data store, disastrous for a cache |
| `allkeys-lru` | Evict least-recently-used across all keys. **The usual choice for a cache** |
| `allkeys-lfu` | Least-*frequently*-used. Better when you have a stable hot set and occasional scans that would pollute an LRU |
| `volatile-lru` / `volatile-ttl` | Only evict keys that have a TTL set. Useful when the same Redis holds cache *and* persistent data |

**LRU vs LFU is a real interview question.** LRU is fooled by a scan: one batch job reading a million cold keys evicts your entire hot set. LFU isn't (it counts accesses, with decay). That's why Redis added LFU.

**TTL is your safety net, not your consistency mechanism.** Even with perfect invalidation, put a TTL on everything — it bounds the blast radius of a missed invalidation, and it's the reason a bug leaves you stale for 5 minutes instead of forever.

**Jitter your TTLs.** If you populate 10,000 keys during a deploy all with `TTL=300`, they all expire in the same second and you get a synchronized miss storm. Use `300 + random(0, 60)`.

---

## 5. The famous failure modes

This is the section interviews actually live in. Naming these correctly is a strong signal.

### Thundering herd / cache stampede

One hot key expires. 5,000 concurrent requests all miss, all hit the database for the same row, simultaneously. The DB — which was comfortably serving 5% of traffic — takes 100% of it in one spike and falls over. **The cache didn't fail; its expiry caused the outage.**

Fixes, in increasing order of sophistication:

1. **Locking / request coalescing.** First caller to miss takes a lock (`SET key:lock 1 NX EX 10`) and does the DB fetch; the others wait briefly and retry the cache. One DB query instead of 5,000.
2. **Probabilistic early expiration (XFetch).** Each reader, as the TTL approaches, has a small and growing chance of deciding to refresh *early*, while the old value is still being served. One unlucky request does the work; nobody ever sees a miss.
3. **Serve stale while revalidating.** Store the value with a logical expiry *inside* it and a much longer physical TTL. On logical expiry, return the stale value immediately and kick off an async refresh. Latency never spikes. This is what CDNs call `stale-while-revalidate`.

```python
def get_with_lock(key: str, loader, ttl: int = 300):
    cached = redis.get(key)
    if cached is not None:
        return json.loads(cached)

    # only one caller wins the lock and touches the DB
    if redis.set(f"lock:{key}", "1", nx=True, ex=10):
        try:
            value = loader()
            redis.setex(key, ttl + random.randint(0, 60), json.dumps(value))
            return value
        finally:
            redis.delete(f"lock:{key}")

    time.sleep(0.05)              # someone else is loading it
    cached = redis.get(key)
    return json.loads(cached) if cached else loader()   # fall back
```

### Cache penetration

Requests for keys that **don't exist anywhere** — every one is a guaranteed miss that reaches the DB. Often malicious (`GET /user/-1` in a loop), and the cache provides zero protection because there's nothing to cache.

Fixes: **cache the negative result** (`user:999 → NULL`, short TTL — 30–60s so a later creation isn't hidden for long), and/or put a **Bloom filter** in front holding all existing IDs (constant memory, no false negatives, so "definitely not present" short-circuits before the DB).

### Cache avalanche

Large numbers of keys expire at once (TTL synchronization) *or* the cache tier itself goes down. The DB gets the full, un-shielded firehose.

Fixes: jitter TTLs; replicate the cache (Redis Sentinel/Cluster) so it's not a SPOF; add a **circuit breaker + rate limiter in front of the database** so a cache outage degrades service instead of destroying it; keep a small in-process L1 as a last line of defence.

### Hot key

One key gets a disproportionate share of traffic (a viral post, a big match). It lives on one Redis shard, and that shard saturates while the rest idle. **Sharding doesn't help — it's one key.** (Same problem as in the partitioning post.)

Fixes: replicate the hot key across N shards under suffixed names (`post:123:0..9`) and read a random one; cache it in-process at the app layer (accept a second of staleness); or put a small local L1 in front of Redis exactly for the top-K keys.

### Big key

A single 50 MB value in Redis. Redis is single-threaded for command execution, so serializing that blocks *every other client*. Split it, or don't cache it.

---

## 6. Invalidation: the actual hard part

> "There are only two hard things in Computer Science: cache invalidation and naming things." — Phil Karlton

The strategies, from weakest to strongest:

1. **TTL only.** Accept bounded staleness. Simple, robust, and correct for a *huge* number of use cases — always ask "how stale is too stale?" before engineering something clever. If the answer is "60 seconds is fine," you're done.
2. **Explicit invalidation on write.** Delete the key when the underlying row changes. Fails when the write path bypasses your app (a batch job, a DBA, another service).
3. **Change Data Capture.** Tail the database's replication log (Debezium on Postgres WAL / MySQL binlog) and invalidate from there. Nothing can bypass it, because the WAL *is* the write. This is the strongest and the one to name if the interviewer pushes.
4. **Versioned keys.** Never invalidate anything; include a version in the key (`user:42:v7`). Writers bump the version. Old entries are unreferenced and get evicted by LRU. Cute, race-free, and costs you memory.

**Cache tagging / group invalidation.** "Invalidate every cached page containing product 99." Redis has no `DELETE WHERE`, and `KEYS *` is an O(N) blocking scan — never run it in production (use `SCAN` if you must). Instead maintain a set: `tag:product:99 → {page:1, page:7, search:foo}`, and on change, read the set and delete its members.

---

## 7. Consistency, honestly

A cache is a replica, so everything from replication applies:

- **A cache makes your system eventually consistent**, whether you meant it to or not. If your product cannot tolerate that, don't cache that data (or use write-through and pay for it).
- **Read-your-own-writes** is the failure users actually notice: I update my profile, the write goes to the DB, my next read hits a stale cache, and my change "didn't save." Fix by invalidating synchronously on the write path *before* returning, or by routing a user's reads around the cache briefly after they write.
- **Ask "what's the cost of stale?"** before designing anything. A stale follower count: nobody dies. A stale account balance or a stale betting price: someone loses money. The answer sets your entire design.

---

## 8. Where this shows up in the odds feed

Cross-linking to the practical case, because this is what makes it stick:

- The **Redis snapshot store** is a cache — but of a specific kind. It doesn't cache *the database*; it caches **the current state of a stream**, materialized by a Kafka consumer group. That inverts the usual pattern: it's a **write-through cache populated by the event log**, so it can't go stale relative to a source of truth (it *is* the source of truth for "current price"), and there's no invalidation problem at all. Deriving the cache from the same ordered log the clients read is what buys that.
- Sequence numbers on each entry are what let a client safely merge snapshot + deltas — the cache-consistency problem is solved by **versioning**, exactly as in §6.4.
- On a mass reconnect (a gateway dies, thousands of clients reconnect at once and all request snapshots), you get a **thundering herd against Redis**. Mitigate with staggered reconnect backoff *on the client*, plus jitter — the same problem as §5, different tier.
- **Reference data** (fixture/market ID mappings, entitlements) is the classic cache-aside case: read constantly, changes rarely, tolerates seconds of staleness. In-process L1 with a short TTL is right here, and it's the one place where "just use a TTL" is the complete answer.

---

## 9. The interview checklist

1. **Why are you caching?** Latency or load-shedding? Name it.
2. **What's the read/write ratio and the access skew?** No skew, no benefit.
3. **What's the cost of stale data?** This drives everything downstream.
4. **Which layer?** Browser / CDN / app-local / distributed / DB. Justify.
5. **Which pattern?** Cache-aside by default; say why if not.
6. **What's the key schema?** `entity:id:version`. Watch out for keys that vary by user and blow up cardinality.
7. **TTL + eviction policy?** `allkeys-lru`, jittered TTL, and a TTL on *everything* as a backstop.
8. **How do you invalidate?** Delete-don't-update, and name CDC if pushed on correctness.
9. **What happens on a cache miss storm?** Stampede/penetration/avalanche — name them and give a fix each.
10. **What happens if the cache dies entirely?** If the answer is "the DB dies too," you've built a hidden SPOF — say so, and add a circuit breaker.

### Quickfire answers

**"Cache-aside vs write-through?"** Aside = lazy, only caches what's read, tolerates staleness, cache failure is survivable. Through = eager, never stale, pays write latency, may cache data nobody reads. Aside is the default.

**"Why delete instead of update the cache on a write?"** Delete is idempotent; concurrent updates can't interleave into a permanently-wrong cached value. Next read repopulates.

**"What's a thundering herd, and how do you stop it?"** One hot key expires, thousands of concurrent misses hit the DB at once. Lock/coalesce, refresh probabilistically before expiry, or serve stale while revalidating.

**"How do you stop lookups for non-existent keys hitting the DB?"** Cache the null (short TTL) and/or a Bloom filter of existing IDs.

**"What if one key is 100× hotter than the rest?"** Sharding won't help — replicate the key under N suffixed names, or cache it in-process. Consistent hashing balances keys, not traffic.

**"LRU or LFU?"** LRU by default; LFU when a periodic scan would otherwise evict your hot set.

**"Is your cache a SPOF?"** Only if your DB can't survive its loss. Test with a cold-cache load test — if the DB folds, the cache is a dependency, not an optimization.

---

## 10. Further reading

- **DDIA, Chapter 5** — replication. A cache is a replica; every staleness anomaly there applies here.
- **Facebook, "Scaling Memcache at Facebook" (NSDI 2013)** — the canonical paper. Leases (their stampede fix), the delete-don't-update rule, and cold-cluster warmup, all from a system at absurd scale.
- **Vattani et al., "Optimal Probabilistic Cache Stampede Prevention" (2015)** — the XFetch early-expiry algorithm.
- **Redis docs on `maxmemory-policy` and key eviction** — short, and the LRU-approximation detail (Redis samples rather than maintaining a true LRU list) is a nice thing to know.


<a name="replication"><a/>
# 8 - Replication & Consistency Models

# Replication & Consistency Models

*Everything you need to hold your own on this topic in a system design interview — starting from first principles.*

---

## 0. First, the vocabulary

Every sentence in this post depends on these words. They get thrown around casually in interviews, so let's pin them down before we need them.

**Node.** One machine (or one process) holding a copy of your data. That's all. "Node," "server," "replica," and "instance" are used almost interchangeably; I'll say *node* for a machine and *replica* for a machine that holds a copy of someone else's data.

**Replication.** Keeping a copy of the same data on multiple nodes. Note what it is *not*: it's not partitioning. Partitioning (the previous post) splits *different* data across machines. Replication puts the *same* data on several machines. Real systems do both: split into 8 shards, then keep 3 copies of each shard.

**Why replicate at all?** Three reasons, and they pull in different directions:
1. **Availability** — if one node dies, another has the data and can serve.
2. **Read throughput** — 5 copies means 5 machines can answer reads.
3. **Latency** — put a copy in São Paulo so Brazilian users don't cross an ocean (~70 ms each way, and no amount of clever code beats the speed of light).

**Source of truth / leader / primary.** The node whose copy is considered authoritative — the one writes go to. Called *leader*, *primary*, or (in older docs) *master*.

**Follower / replica / secondary.** A node that receives a stream of changes from the leader and applies them to its own copy. Reads can be served from here. Writes cannot.

**Replication lag.** The time between "the leader accepted a write" and "this particular follower has applied it." During that window, the follower is **stale** — it will happily answer a read with old data and give you no warning that it did so. Lag is usually milliseconds. Under load, it can be seconds or minutes. **Almost every consistency bug you will ever debug lives inside this window.**

**Consistency.** In this context: *what guarantees do you get about which version of the data a read returns?* That's the whole question. Not "is the data correct" (that's ACID's C, a different thing — see §7).

**Network partition.** The network between two groups of nodes breaks. Both groups are alive and healthy; they just can't talk to each other. Each may believe the other is dead. This is not a rare exotic event — it's a switch reboot, a bad config push, a cross-AZ blip. Assume it will happen.

That's the toolkit. Now the actual content.

---

## 1. Single-leader replication (the default everywhere)

The shape almost every relational database uses out of the box — Postgres, MySQL, SQL Server, and also MongoDB and Kafka (per partition).

```
            writes
              │
              ▼
        ┌───────────┐
        │  LEADER   │───── change stream ──┐
        └───────────┘                      │
              │                            ▼
              └── change stream ──┐   ┌──────────┐
                                  │   │FOLLOWER 1│◄── reads
                                  ▼   └──────────┘
                            ┌──────────┐
                            │FOLLOWER 2│◄── reads
                            └──────────┘
```

**The rules:**
- All writes go to the leader. Only the leader.
- The leader writes the change to its local log and sends that change to every follower.
- Followers apply the changes **in the same order** and can serve reads.

**Why this works so well:** there is exactly one place where write order is decided, so there is never a conflict about "which write happened first." That single-writer property is doing an enormous amount of work, and it's why this design dominates.

### What actually gets shipped to the followers

Worth knowing, because it comes up:

- **Statement-based** — send the SQL. Cheap, but broken: `UPDATE ... SET t = NOW()` produces a different result on each node. Mostly abandoned.
- **Write-ahead log (WAL) shipping** — send the physical, byte-level changes the leader made to its own storage. What Postgres does. Very efficient, but tightly coupled to the storage engine version (you generally can't have a replica on a different major version, which makes zero-downtime upgrades painful).
- **Logical / row-based** — send "row 42 in table users changed from X to Y." Decoupled from storage internals, so it can be consumed by *other systems*. This is what **Change Data Capture** (CDC, e.g. Debezium) reads to push database changes into Kafka.

### Synchronous vs asynchronous — the trade-off that matters

When the leader gets a write, when does it tell the client "done"?

**Asynchronous:** leader writes locally, replies "OK" immediately, sends to followers whenever.
- ✅ Fast. The client never waits for a follower.
- ❌ **If the leader dies before the change reaches any follower, the write is gone forever** — even though you told the client it succeeded. This is *not* a theoretical concern; it's a routine cause of data loss during failover.

**Synchronous:** leader waits for a follower to confirm it has the change, *then* replies "OK."
- ✅ At least one other machine definitely has your data. Failover loses nothing.
- ❌ Every write now costs an extra network round-trip. And worse: **if that follower is slow or down, all writes block.** You've made your availability *worse* by adding a machine.

**The pragmatic answer, used by almost everyone: semi-synchronous.** Make exactly *one* follower synchronous and the rest async. You get "the data exists in at least two places" without one flaky replica in Frankfurt being able to halt your writes. If the sync follower goes down, another one is promoted to sync.

If you say only one thing about this in an interview, say: *"async replication means acknowledged writes can be lost on failover; that's the price of the fast path, and semi-sync is how you buy the important part of the guarantee back."*

### Failover: where systems actually break

The leader dies. Now what?

1. **Detect it.** Usually a timeout — no heartbeat for N seconds. Here's the problem, and it's fundamental: **you cannot distinguish "the leader is dead" from "the leader is slow" from "I can't reach the leader."** A 30-second GC pause looks exactly like death.
2. **Choose a new leader.** Either by an election among the nodes (see *consensus*, §6) or by an external controller. You want the follower with the most up-to-date data.
3. **Reconfigure.** Clients and remaining followers must all now agree on who the leader is.

**Three ways this goes wrong — worth naming all three:**

- **Lost writes.** With async replication, the new leader may be missing the last few writes the old leader had acknowledged. If you promote it anyway, those writes silently vanish. If the old leader comes back and still has them, they now *conflict* with new writes. The usual fix is brutal: discard the old leader's extra writes.
- **Split-brain.** The old leader isn't actually dead — it was just unreachable. Now you have **two nodes both accepting writes**, both convinced they're the leader. Data diverges, and you can't merge it back. This is the nightmare scenario. The defence is **fencing** (§6).
- **Timeout tuning.** Too short → you fail over on a hiccup, causing an outage to prevent one that wasn't happening. Too long → you're down for a minute before anyone notices. There is no correct value, only trade-offs.

---

## 2. The problems replication lag creates (and how users experience them)

This is the heart of the post. Three named anomalies. Interviewers love them because each one is a *concrete user complaint* that maps to a *concrete guarantee*.

### Anomaly 1: "I saved it and it disappeared"

You update your profile bio. The write goes to the leader. Your page reloads, the read is load-balanced to a follower that hasn't caught up yet, and you see your *old* bio. To you, the save failed. You do it again. Now you're angry.

**The guarantee that fixes it: read-your-own-writes** (a.k.a. read-after-write consistency). *You* always see *your own* writes. Everyone else may see stale data — that's fine, they don't know what you just typed.

How to implement it (know at least two):
- For anything the user could have modified, **read from the leader**. (E.g. always read your *own* profile from the leader, other people's from a follower.)
- **Remember the write timestamp/position** (client-side or in the session). For the next N seconds after a write, route that user's reads to the leader, or to a follower that has caught up past that position.

### Anomaly 2: "Time went backwards"

You refresh a comment thread. The first read hits an up-to-date follower and shows a new comment. You refresh again; this read hits a *lagging* follower, and the comment **disappears**. Data appeared to travel backwards in time.

**The guarantee that fixes it: monotonic reads.** You may see stale data, but you'll never see data get *older* than what you already saw.

Implementation: make sure a given user always reads from the *same* replica (e.g. hash their user ID to pick one — and yes, that's consistent hashing from the previous post doing a completely different job). Caveat: if that replica dies you must reroute, and then you've broken the guarantee anyway.

### Anomaly 3: "The answer came before the question"

Two writes are causally related: Alice asks "how are you?", Bob replies "fine." A third observer reads from a replica that received Bob's reply but not yet Alice's question, and sees an answer to nothing.

**The guarantee that fixes it: consistent prefix reads.** If writes happened in a certain causal order, anyone reading them sees them in that order. This one is mostly a *partitioned* problem — within one partition, order is preserved; across partitions, there's no global ordering unless you build one. (This is exactly why the odds feed partitions by `market_id`: it needs prices for *one market* ordered, and doesn't need — or want — a global order across all markets.)

---

## 3. Multi-leader replication

What if writes could go to *more than one* leader? Now you can accept writes in multiple datacenters (fast local writes), or keep working offline (your phone's calendar app is effectively a leader).

The price is unavoidable and severe: **two leaders can accept conflicting writes to the same record, and nobody notices until they sync.** In single-leader, that's impossible by construction. Here, it's the defining problem.

**Conflict resolution strategies:**
- **Last-write-wins (LWW).** Attach a timestamp; highest wins. Simple, extremely popular, and **it silently discards data** — and it depends on clocks from different machines being comparable, which they are not (see §5). Fine for a cache, terrible for a shopping cart.
- **Conflict-free replicated data types (CRDTs).** Data structures designed so that concurrent changes *merge* deterministically with no conflict possible (counters, sets, sequences). This is how Google Docs–style collaborative editing works.
- **Ask the application (or the user).** Store both versions and resolve on read — Amazon's shopping cart famously merged conflicting carts by taking the union, which meant deleted items could reappear. They judged that a better failure than losing an item.

**Interview line:** *"Multi-leader buys you local writes and offline capability, and pays for it with conflicts. Only take that deal if you actually need one of those two things."*

---

## 4. Leaderless replication (Dynamo-style)

Used by Cassandra, Riak, and Amazon's original Dynamo. **There is no leader.** The client (or a coordinator on its behalf) writes to *several* nodes at once and reads from *several* nodes at once.

### Quorums — the actual mechanism

Three numbers:
- **N** = how many replicas hold each piece of data (e.g. 3)
- **W** = how many must *acknowledge a write* before it counts as successful
- **R** = how many must *respond to a read* before you accept the answer

**The rule: if `W + R > N`, then any read set and any write set must overlap in at least one node** — so at least one node you read from is guaranteed to have the latest write. Since every value carries a version, you can tell which of the returned copies is newest and use it.

Concretely, with N=3:
- `W=3, R=1` — writes are slow and fragile (one node down blocks writes), reads are fast. Good for read-heavy, rarely-written data.
- `W=1, R=3` — the mirror image.
- **`W=2, R=2`** — 2+2 > 3 ✓. Tolerates *one* node being down for both reads and writes. This is the standard, balanced choice.
- `W=1, R=1` — 1+1 = 2, not > 3 ✗. Fast, highly available, **and eventually consistent** — you can absolutely read stale data. Sometimes exactly what you want.

The beauty of it: **consistency is a per-query dial, not a database-wide decision.** Cassandra lets you set it per statement. Read a user's session? `ONE`. Read their account balance? `QUORUM`.

Two repair mechanisms make the stale nodes eventually catch up:
- **Read repair** — when a read gets back different versions from different replicas, the coordinator writes the newest one back to the stale ones. Fixes frequently-read data for free.
- **Anti-entropy** — a background process compares replicas (using Merkle trees, so it doesn't have to send everything) and fixes differences. Fixes data nobody reads.

**Sloppy quorum & hinted handoff** (name-drop these): if the *right* N nodes for a key aren't reachable, write to *some other* N nodes temporarily so the write succeeds anyway; they hold the data as a "hint" and forward it home once the proper nodes return. Great for availability, and it means `W + R > N` no longer guarantees what you think it does — the overlapping node might not be one of the "home" nodes at all. This is why quorums are *not* the same as strong consistency.

---

## 4b. So which model, when?

| | Writes go to | Conflicts possible? | Typical use |
|---|---|---|---|
| **Single-leader** | 1 node | No | Default. Postgres, MySQL, MongoDB, Kafka partitions |
| **Multi-leader** | Several nodes | Yes — must resolve | Multi-datacenter writes, offline-capable clients |
| **Leaderless** | Many nodes at once | Yes — versioned & repaired | High availability, tunable consistency. Cassandra, Dynamo |

---

## 5. Why "which write was later?" is a genuinely hard question

Instinct says: timestamp the writes and take the newer one. This fails, and the reason is worth internalizing.

**Physical clocks on different machines disagree.** NTP keeps them within tens of milliseconds — *usually*. Under network load, or on a VM whose clock drifts, skew can be hundreds of milliseconds or worse. Clocks can also jump *backwards* (an NTP correction). So "write A has timestamp 10:00:00.100 and B has 10:00:00.050, therefore A is later" can simply be false. Last-write-wins built on wall clocks quietly loses data. This is a real bug class, not a purist's complaint.

**The fix: logical clocks — count events, don't read the wall.**

- **Lamport timestamps**: a counter per node, exchanged with every message and set to `max(local, received) + 1`. Gives a total order consistent with causality — but you can't tell "concurrent" apart from "ordered."
- **Version vectors**: one counter *per node*, all carried together. These *can* detect concurrency: if neither version vector dominates the other, the two writes were genuinely concurrent and there's a real conflict to resolve. This is what Dynamo-style systems use to decide whether they can auto-pick a winner or must hand you both versions ("siblings").
- **Hybrid logical clocks (HLC)**: combine a physical timestamp with a logical counter, so you get something roughly human-readable *and* causally correct. Used by CockroachDB. (Spanner takes the other path: give every datacenter an atomic clock and a GPS receiver, bound the uncertainty explicitly, and *wait it out* — the famous `commit-wait`. Google can afford that; you can't.)

**Interview line:** *"You can't order distributed events by wall-clock time. You need logical clocks — version vectors if you need to detect true concurrency."*

---

## 6. Consensus, briefly (and why you should mostly not build it)

**Consensus** = getting a group of nodes to agree on one value, even when some are slow, crashed, or unreachable. Nearly every hard distributed problem reduces to it: *who is the leader?* *did this transaction commit?* *what's the current cluster membership?*

**Raft** (and its ancestor Paxos) is the standard algorithm. The mental model is enough:
- Nodes elect a leader by majority vote.
- The leader appends entries to a replicated log; an entry is **committed** once a **majority** of nodes have it.
- If the leader dies, a new election happens. Because commitment required a majority, and any two majorities overlap, the new leader is guaranteed to already have every committed entry. **Nothing committed can be lost.**

**Why the majority matters:** a partitioned minority can never form a majority, so it can never elect a leader or commit anything. **That's what structurally prevents split-brain** — and it's why these systems need an odd number of nodes (3, 5) and can only tolerate `(n-1)/2` failures.

**Fencing tokens** — the one detail worth memorizing. Every time a new leader is elected, it gets a monotonically increasing number. Every write it sends carries that number, and storage rejects any write carrying a number lower than the highest it has seen. Now, when a zombie old leader wakes up from its GC pause and tries to write, its stale token gets rejected. *Without fencing, a leader lease/lock does not actually protect you* — the old leader can still be mid-flight when the lease expires.

**What to actually do:** don't implement consensus. Use something that already has: **etcd**, **ZooKeeper**, **Consul**, or the consensus built into your database (Kafka's KRaft, CockroachDB's Raft groups). Recognizing that a problem *needs* consensus, and reaching for the right tool, is the skill — not writing Raft.

---

## 7. CAP, PACELC, and the words people misuse

You have all the pieces now, so this finally makes sense.

**CAP theorem, stated properly:** in a distributed system, **when a network partition occurs**, you must choose between:
- **Consistency (CP)**: refuse to serve requests you can't guarantee are correct. Reject the write, return an error. *Correct but unavailable.*
- **Availability (AP)**: answer anyway, with possibly-stale data, and reconcile later. *Available but possibly wrong.*

**The single most important correction to how CAP is usually taught: you do not "pick 2 of 3."** Partition tolerance is not optional — networks fail whether you consent or not. **A "CA system" is just a single-node database.** The real question is always: *when the network breaks, do you sacrifice correctness or availability?*

**PACELC** is the better framing, because partitions are rare and you're still making trade-offs the other 99.9% of the time:

> **If Partition** → choose **A**vailability or **C**onsistency; **Else** → choose **L**atency or **C**onsistency.

That "else" clause is exactly the sync-vs-async replication trade-off from §1. Waiting for replicas costs latency; not waiting costs consistency. Every day, not just on bad days.

- Cassandra / Dynamo: **PA/EL** (available under partition, low-latency otherwise — stale reads either way, unless you dial the quorum up)
- HBase / Spanner: **PC/EC** (consistency over both availability and latency)
- DynamoDB: **PA/EL** by default, **PC/EC** if you request strongly consistent reads

**The vocabulary trap, and it *is* asked:** **the C in CAP is not the C in ACID.**
- CAP's C = **linearizability**: the system behaves as if there were one single copy of the data and every operation happened instantaneously at one point in time. It's about *recency of reads across replicas.*
- ACID's C = **consistency** as in "the database doesn't violate your constraints" (no negative balances, foreign keys hold). It's about *application invariants on one node.*

They are unrelated ideas that unluckily share a letter.

### The consistency ladder (strongest to weakest)

| Model | The promise | Cost |
|---|---|---|
| **Linearizable / strong** | There is one copy. Every read sees the latest write. | Needs consensus; coordination on every op; unavailable under partition |
| **Sequential** | Everyone sees operations in the same order — but maybe not instantly | Cheaper |
| **Causal** | Causally-related events are seen in order; concurrent ones may differ per observer | **Available under partition — the strongest model that is.** Often the sweet spot |
| **Eventual** | If writes stop, all replicas converge... eventually. Says nothing about *when*. | Cheapest, most available, most surprising |

"Eventual consistency" is a famously weak promise — it doesn't tell you *how* stale, or bound it at all. Whenever you can, name a *specific* guarantee instead (read-your-own-writes, monotonic reads, causal). That's the vocabulary that shows you've thought about it.

---

## 8. Where this shows up in the odds feed

- **Kafka is single-leader replication, per partition.** Each partition has one leader broker and followers; the **ISR** (in-sync replicas) is the set that has caught up. `acks=all` means "don't acknowledge my write until all in-sync replicas have it" — that's **synchronous replication**, and it's the right setting when a lost odds update means a client trades on a stale price. `acks=1` is async: faster, and it can lose writes when a broker dies. This is §1, verbatim.
- **`min.insync.replicas`** is a quorum. Set it to 2 with replication factor 3 and you get: tolerate one broker failure, refuse writes if two are down. That's an explicit, deliberate **CP** choice — the system stops accepting data rather than accepting data it might lose.
- **Consumer lag is replication lag** wearing a different hat: how far behind the head of the log a consumer is. If the WebSocket gateways lag, clients get stale prices — which is *precisely* the anomaly in §2, just at the application tier. This is why it's the alarm that matters.
- **Ordering guarantee:** per-market ordering (consistent prefix reads, §2 anomaly 3) is required — a client must never see a price move backwards. Global ordering across markets is *not* required, and demanding it would kill scalability. Knowing which guarantee you need, and no more, is the whole game.
- **Clock skew (§5) is a live problem here:** 300 bookmakers each stamp their own updates. Their clocks disagree. "Which price is newer?" cannot be answered by comparing their timestamps — you need your *own* ingest sequence, per market, which is exactly what the Kafka partition offset gives you.

---

## 9. The interview checklist

1. **Replication or partitioning?** Be explicit: same data on many nodes (replication) vs different data on many nodes (partitioning). Most systems need both.
2. **Why are you replicating?** Availability, read throughput, or geographic latency. They lead to different designs.
3. **Which model?** Single-leader by default. Justify multi-leader (multi-DC writes / offline) or leaderless (max availability, tunable) if you go there.
4. **Sync or async?** Name the trade: async can lose acknowledged writes on failover; sync blocks on a slow replica. Semi-sync is the usual compromise.
5. **What happens when the leader dies?** Detection (timeouts), election, and the three hazards: lost writes, split-brain, bad timeouts.
6. **How do you prevent split-brain?** Majority quorum + **fencing tokens**. Say both.
7. **What staleness anomalies can users hit?** Read-your-own-writes, monotonic reads, consistent prefix. Give the user-visible symptom, then the fix.
8. **If leaderless: what are N, W, R?** State them, and state that `W+R > N` gives overlap. `N=3, W=2, R=2` is the default answer.
9. **How do you order concurrent writes?** *Not* wall clocks. Logical clocks / version vectors.
10. **CAP:** frame it as "under partition, do I sacrifice consistency or availability?" — never as "pick 2 of 3." Then upgrade to PACELC.

### Quickfire answers

**"Replication vs partitioning?"** Replication = copies of the *same* data on several nodes (availability, read scale). Partitioning = *different* data on different nodes (storage and write scale). Orthogonal; used together.

**"What's replication lag?"** The window between the leader accepting a write and a follower applying it. During it, that follower serves stale reads and doesn't tell you.

**"What's wrong with async replication?"** Writes you already acknowledged to the client can be lost if the leader dies before they propagate.

**"What's split-brain, and how do you prevent it?"** Two nodes both believe they're the leader and both accept writes; the data diverges irreconcilably. Prevent with majority quorums (a partitioned minority can't elect a leader) plus fencing tokens (storage rejects writes from a stale leader).

**"What does `W + R > N` guarantee?"** That the read set and write set overlap in at least one node, so a read sees at least one copy of the latest write. Caveat: sloppy quorums break this.

**"A user saves their profile and sees the old one. What's the bug and the fix?"** Read hit a lagging follower — you're missing read-your-own-writes. Route that user's reads to the leader for a short window after a write.

**"Can you order events by timestamp?"** No — clocks on different machines disagree and can jump backwards. Use logical clocks; version vectors if you need to detect true concurrency.

**"CAP — pick two?"** No. Partitions aren't optional. Under partition you choose C or A; the rest of the time you're trading latency against consistency (PACELC).

**"Is the C in CAP the C in ACID?"** No. CAP's C is linearizability (reads see the latest write across replicas). ACID's C is "constraints aren't violated." Same letter, unrelated concepts.

**"What's the strongest consistency model you can have while staying available under a partition?"** Causal consistency.

---

## 10. Further reading

- **DDIA, Chapters 5 and 9** — replication, and then "Consistency and Consensus." Ch. 9 is the hardest chapter in the book and the highest-value one for interviews.
- **Raft paper / raft.github.io** — the visualization there teaches leader election in about ten minutes. Far more approachable than Paxos.
- **Dynamo (2007)** — quorums, sloppy quorums, hinted handoff, version vectors, all in one readable paper.
- **Kyle Kingsbury's "Jepsen" write-ups** — real databases, tested against real partitions, breaking in ways their docs said they wouldn't. Sobering and genuinely fun.



<a name="solid,kiss"><a/>
# 1 - SOLID, KISS, ACID....

Software design principles and database properties are like the unwritten rules of good engineering. They keep your code from turning into a chaotic, unmaintainable mess and ensure your application doesn't drop data when millions of users hit it at once.

Here is a breakdown of the big ones—**SOLID**, **KISS**, **DRY**, and **ACID**—and how they actually show up in a software engineer’s daily routine.

---

## 1. SOLID Principles

SOLID is an acronym for five object-oriented design principles aimed at making code readable, flexible, and easy to maintain.

### **S — Single Responsibility Principle (SRP)**

> *A class or module should have one, and only one, reason to change.*

* **What it means:** Each piece of code should do **one job**.
* **Day-to-day application:** When building a user sign-up feature, you don't put database logic, email sending, and password validation all in one giant `UserController` class. Instead, you create a `UserService`, an `EmailService`, and a `Validator`. If the email provider changes later, you only touch the `EmailService`.

### **O — Open/Closed Principle (OCP)**

> *Software entities should be open for extension, but closed for modification.*

* **What it means:** You should be able to add new functionality without rewriting existing, tested code.
* **Day-to-day application:** Imagine processing payments. Instead of using a giant `if/else` statement checking for `CreditCard`, `PayPal`, or `Crypto`, you define a payment interface. When marketing decides to add `Apple Pay`, you simply create a new `ApplePayProcessor` class without modifying the existing payment engine.

### **L — Liskov Substitution Principle (LSP)**

> *Subtypes must be substitutable for their base types.*

* **What it means:** If code expects a parent class, passing in a child class shouldn't break the application.
* **Day-to-day application:** If you have a base class called `Bird` with a `fly()` method, you shouldn't create a `Penguin` subclass that throws a `CannotFlyException`. In daily code reviews, engineers catch this by ensuring subclasses don't unexpectedly override or break base behaviors.

### **I — Interface Segregation Principle (ISP)**

> *Clients shouldn't be forced to depend on interfaces they do not use.*

* **What it means:** Keep interfaces small and specific rather than large and multi-purpose.
* **Day-to-day application:** Instead of creating a massive `Worker` interface with methods like `code()`, `design()`, and `manage()`, split them into `Codable`, `Designable`, and `Manageable`. A backend developer class only implements `Codable`, so it isn't forced to write empty methods for `design()`.

### **D — Dependency Inversion Principle (DIP)**

> *Depend upon abstractions, not concretions.*

* **What it means:** High-level code shouldn't rely directly on low-level details. Both should rely on shared interfaces.
* **Day-to-day application:** When writing unit tests, you don't want your test code hitting a live SQL database. By using DIP, your core app depends on a generic `DatabaseRepository` interface. In production, you inject a `PostgreSQLRepository`; in tests, you inject a `MockRepository`.

---

## 2. General Engineering Principles

### **KISS — Keep It Simple, Stupid**

* **What it means:** Avoid over-engineering. The simplest solution is usually the best one.
* **Day-to-day application:** You need to filter a list of 100 items. Instead of implementing a complex, custom thread-pooling search algorithm because it looks cool, you use standard library methods (like `.filter()`). During code reviews, engineers frequently ask, *"Can we make this simpler?"*

### **DRY — Don't Repeat Yourself**

* **What it means:** Every piece of knowledge or logic must have a single, unambiguous representation in a system.
* **Day-to-day application:** If you find yourself copying and pasting a 10-line date-formatting function into three different UI components, stop. Move that logic into a single helper function (`formatDate()`) and import it. If the date format requirement changes, you update it in one place instead of hunting down three copies.

---

## 3. ACID Properties (Database Reliability)

Unlike SOLID, KISS, and DRY (which govern code design), **ACID** governs **database transactions** to ensure data integrity.

### **A — Atomicity ("All or Nothing")**

* **Application:** Transferring $50 from User A to User B requires two steps: deducting $50 from A, and adding $50 to B. If step two fails, Atomicity rolls back step one so User A doesn't lose money into thin air.

### **C — Consistency**

* **Application:** Your database has rules (e.g., balance cannot be negative). A transaction will only succeed if the end state respects all database constraints, foreign keys, and rules.

### **I — Isolation**

* **Application:** If two users try to buy the very last seat on a flight at the exact same millisecond, Isolation ensures the transactions run independently so the seat isn't double-booked.

### **D — Durability**

* **Application:** Once a transaction is committed (e.g., "Payment Successful"), that state is permanently saved to storage. Even if the server loses power a microsecond later, the transaction remains intact when power returns.

---

## How They Fit Together Daily

When a software engineer gets a ticket to build a new feature:

1. They apply **KISS** to keep the initial solution straightforward.
2. They structure their classes using **SOLID** so the code is clean and easy to test.
3. They extract reusable utilities to stay **DRY**.
4. They wrap critical database interactions in an **ACID** transaction so user data stays safe.
