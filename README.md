# System_Design_Overvieew

# Concepts

1 - [CAP Theorem](#cap-theorem-section)

2 - [OSI Model](#osi-model)

3 - [TCP/IP Model](#tcp/ip-model)

4 - [ACID and SQL/NoSQL](#acid-sql-nosql)

5 - [Communication in Real-Time - WebSockets](#websockets)

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

