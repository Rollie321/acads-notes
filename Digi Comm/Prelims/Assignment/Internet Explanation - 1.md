# How the Internet Works — Diorama Reference Notes

*Topic: What happens when you search online or talk to an AI chatbot, explained through the Shannon-Weaver communication model.*

---

## 1. The Communication Model (Core Framework)

The **Shannon-Weaver Model** (1948, Claude Shannon & Warren Weaver) is the classic engineering model of communication. It has 6 core parts, and it maps almost perfectly onto how the internet moves information.

| Component | Definition | Web Search Example | AI Chatbot Example |
|---|---|---|---|
| **Source** | The origin of the message/idea | You, thinking of a question | You, thinking of a prompt |
| **Transmitter (Encoder)** | Converts the message into a signal that can travel | Browser turns your query into an HTTP request | App turns your prompt into a data request (JSON) |
| **Channel** | The medium the signal travels through | Wi-Fi → ISP → fiber-optic/undersea cables → data center | Same physical channel, but routed to an AI server cluster |
| **Noise** | Anything that distorts or interrupts the message | Typos, weak signal, packet loss, server downtime | Vague prompts, dropped connection, model errors, overloaded servers |
| **Receiver (Decoder)** | Converts the signal back into a usable message | Search engine server reads/decodes your query | AI server decodes your prompt into tokens |
| **Destination** | Where the message is ultimately interpreted/used | The ranking algorithm, then back to you | The language model, then back to you |
| **Feedback** | The response that completes the loop | You click a result or refine your search | You reply, follow up, or correct the AI |

**Key idea to explain on your plaque:** communication isn't just "sending a message" — it's a full loop of encoding, transmitting, decoding, and interpreting, and *noise* can appear at any point along the way.

---

## 2. Under the Hood: Web Search

1. **DNS Lookup** — Your browser doesn't know where "google.com" physically is, so it asks a DNS (Domain Name System) server to translate the name into an IP address (like looking up a phone number for a name).
2. **Request Travels the Channel** — The request leaves your device, passes through your router, your ISP (Internet Service Provider), and travels across fiber-optic/undersea cables to a data center.
3. **The Index, Not the Live Web** — Search engines don't search the internet in real time. They query a massive **pre-built index**, like a giant library catalog, built in advance by automated programs called **web crawlers (bots)** that constantly scan and copy web pages.
4. **Ranking Algorithm** — The search engine scores all matching pages using signals like keywords, page popularity (backlinks), freshness, and your location/history.
5. **Response Sent Back** — Results are packaged as HTML and sent back through the same channel, then your browser renders them as the page you see.

**Diorama-friendly visual:** tiny "spider" bots crawling a web of pages feeding into a card-catalog/index box, connected by a lit-up path back to your device.

---

## 3. Under the Hood: AI Chatbots (LLMs)

1. **Tokenization** — Your prompt is broken into small chunks called **tokens** (roughly word-pieces, not full words).
2. **Embeddings** — Each token is converted into a list of numbers (a vector) that represents its meaning and relationship to other words.
3. **Neural Network Processing** — These numbers pass through a **transformer model** running on powerful GPU clusters in a data center. Unlike search, the model usually isn't looking anything up live — it's generating a response based on patterns learned during training.
4. **Next-Token Prediction** — The model predicts the most probable *next* token, one at a time, repeatedly, until a full response is formed. This is why AI responses often appear to "type out" gradually.
5. **Response Streamed Back** — The generated tokens are converted back into words and sent to your device to display.

**Diorama-friendly visual:** a glowing "brain" or node-and-wire web (yarn/wire sculpture) representing layers of a neural network, contrasted against the crawler/index scene from search.

---

## 4. Search vs. AI Chatbot — Quick Comparison

| Aspect | Web Search | AI Chatbot |
|---|---|---|
| Source of answer | Existing web pages (indexed) | Patterns learned during training (generated, not retrieved) |
| Process | Match & rank existing documents | Predict & generate new text token by token |
| Speed dependency | Depends on index freshness | Depends on model size / server load |
| Main "receiver" component | Ranking algorithm | Neural network (transformer) |
| Common noise/errors | Broken links, outdated pages, ambiguous keywords | Hallucinations, vague prompts, lost context |

---

## 5. Key Vocabulary (for labels/plaques)

- **Packet** — A small unit of data; large messages are broken into packets, sent separately, and reassembled at the destination.
- **IP Address** — A unique numerical address identifying a device on a network.
- **DNS (Domain Name System)** — Translates human-readable names (google.com) into IP addresses.
- **ISP (Internet Service Provider)** — The company that connects your home/device to the wider internet.
- **Data Center** — A facility housing servers that store data and run services (search indexes, AI models).
- **Token** — A chunk of text (word or sub-word) that an AI model reads and generates.
- **Neural Network / Transformer** — The architecture behind modern AI chatbots that processes tokens and predicts responses.
- **Latency** — The delay between sending a request and receiving a response.
- **Noise (in this model)** — Any interference that distorts or blocks the message, whether physical (weak signal) or conceptual (ambiguous wording).

---

## 6. Suggested Diorama Stations (Physical Layout)

1. **Source/Transmitter** — Figure at a laptop/phone.
2. **Channel** — Winding wire/LED path labeled "Wi-Fi → ISP → Fiber Cable → Data Center."
3. **Noise** — Visual disruptions along the path (cracked wire, storm cloud, "404" sign, lightning bolt).
4. **Receiver A (Search)** — Mini server rack + crawler bots + index/catalog box.
5. **Receiver B (AI)** — Mini server rack + glowing node-web "brain" sculpture.
6. **Destination/Feedback** — Same figure receiving the response, completing the loop.
7. **Legend Plaque** — The Shannon-Weaver table from Section 1.

---

## 7. Reference Videos

- Shannon-Weaver Model Explained: https://www.youtube.com/watch?v=7O8JHyaXZkg
- How The Internet Works (ft. Vint Cerf): https://www.youtube.com/playlist?list=PLzdnOPI1iJNfMRZm5DDxco3UdsFegvuB7
- How Does the Internet Work? (Vox): https://www.youtube.com/watch?v=TNQsmPf24go
- Large Language Models Explained Briefly (3Blue1Brown): https://www.youtube.com/watch?v=LPZh9BOjkQs
- Intro to Large Language Models (Andrej Karpathy): https://www.youtube.com/watch?v=zjkBMFhNj_g