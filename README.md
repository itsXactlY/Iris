# Iris Messenger

**Iris — goddess of messages between worlds.**

> *"Lets suppose that you were able every night to dream any dream you wanted to dream, and you would naturally as you began on this adventure of dreams, you would fulfill all your wishes. You would have every kind of pleasure, you see, and after several nights you would say, well that was pretty great, but now lets have a surprise, lets have a dream which isn't under control. Well something is going to happen to me that i don't know what it's gonna be. Then you would get more and more adventurous, and you would make further and further out gambles as to what you would dream, and finally you would dream where you are now."*
>
> — Alan Watts

Iris is what you get when you keep asking a messenger what it actually needs.

It did not need a central server, so the server went. The gateway did not need a public IP, so it moved behind Tor. It did not need an installation, so it became a single binary. That binary did not need to belong to one machine, so the whole thing became portable.

Somewhere along the way it stopped being a messaging app and became a rather elaborate argument with the network.

---

## What it is

Private, end-to-end encrypted messaging without a messaging service.

No accounts. No central message database. No company in the middle that knows who is talking to whom.

Every Iris instance is a complete endpoint. It holds its own identity, its own keys and its own conversations. Everything between two endpoints only ever carries sealed envelopes.

---

## The architecture

Iris follows one rule:

> **Keep state close to the thing that owns it, and make the infrastructure between endpoints as stupid, disposable and uninformed as possible.**

Everything else falls out of that.

### The endpoint owns what matters

Identity, keys and conversation state live on your device, whether that is a browser, a phone or a gateway. Messages are encrypted before they leave it. Nothing in the middle is ever the source of truth.

### The network is only a road

Gateways, relays and queues move ciphertext. They can hold a message for a peer who is offline and deliver it when that peer returns, without being able to read it. Whoever compromises the infrastructure gets envelopes, not content.

### Who you are is not where you are

Iris keeps three questions apart that most systems merge into one:

```text
Who are you?              →  cryptographic identity
Where can I reach you?    →  transport endpoint (an onion address)
Who am I connecting to?   →  pinned gateway key
```

Change your network, your carrier or your machine. The identity stays the same.

### Nobody has to be reachable

Every gateway publishes itself as a Tor onion service, and Tor is built in. Both sides only make outbound connections, so there is no port forwarding, no public IP and nothing to configure. It works behind home routers, carrier NAT, LTE and Starlink.

Other transports exist for places where Tor does not fit (a shared bus, a DHT, peer relays). The rule is the same for all of them: they carry, they do not read.

### Failure is normal

Phones sleep. Networks drop. Processes crash. Iris assumes all of it. Messages queue, delivery retries, and the relationship between two identities survives whatever happens to the connection between them.

---

## Pairing

There is no directory and no server that introduces people.

One person hands the other a handshake link. It contains everything needed to start an encrypted session: who they are, and where they can be reached. Both sides can then compare safety numbers to confirm they are talking to the right person.

```text
Alice ── handshake ──▶ Bob
                        ├── verify identity
                        └── start encrypted session
```

The network is transport. The relationship belongs to the endpoints.

---

## The Box

At some point a distributed system stops looking like infrastructure and becomes an object.

```text
iris-messenger/
├── iris-messenger     # the binary, Tor included
└── iris-data/         # identity, keys, conversations
```

```bash
./iris-messenger --port 9090
```

The folder is the thing. Copy it to another machine and the identity moves with it. No hidden state elsewhere, no export format, no installer.

The same endpoint runs as a single binary, as a rootless container deployment, in the browser, and on Android, where the entire gateway lives on the phone instead of on a server the phone talks to.

The gateway is disposable. The identity is not. Protect the folder accordingly.

---

## What Iris does not promise

Iris does not claim that the network knows nothing. Any transport reveals that traffic exists, when, and how much. Tor narrows what an observer can link to you; it does not abolish traffic analysis.

The actual promise is narrower and concrete:

> **The infrastructure carrying an Iris message never needs the plaintext or the keys to transport it.**

Iris will not save you from a compromised device, a malicious app, a screenshot, social engineering, or losing your identity folder without a backup. If somebody owns the endpoint, they are already standing inside the castle.

There are still machines, state and networking. The difference is who owns them and how much they are required to know.

---

## Under the hood

Iris uses established building blocks instead of inventing its own cryptography: X3DH for session setup, Double Ratchet for ongoing messages, AES-256-GCM, and sender keys for groups. This is the same protocol family used by modern secure messengers.

Supported today: messages, receipts, typing indicators, file attachments, groups, multi-device, offline queuing.

---

## A family of accidents

Iris was not on a roadmap. It grew beside two other projects:

* **MazeMaker** applies the same instinct to memory.
* **Daedalus** applies it to local agents and their context.
* **Iris** applies it to communication.

They are not three layers of one product, and they share no library. They share a refusal: don't put state somewhere just because a server is convenient, and don't make infrastructure authoritative just because it sits in the middle.

---

## Status

Iris is past proof of concept. The architecture works end to end across the standalone gateway, container deployment, Tor federation, Web UI and Android.

Some things are still being measured rather than declared solved: latency under sustained load, recovery after full data loss, and traffic-analysis resistance beyond what Tor provides. If it has not been measured, this README will not pretend it has.

---

## License

PolyForm Noncommercial 1.0.0. See `LICENSE`, `LICENSE-POLYFORM-NC-1.0.0.md` and `NOTICE`. Commercial use requires a separate license.

## Acknowledgments

Signal Protocol, JackrabbitDLM, Nuitka, Caddy, Podman.

---

No account. No central server. No cloud backend waiting patiently to become somebody else's database.

Just an endpoint, an identity, a little transport machinery, and two people trying to send a message across the world.

**Apparently that was enough.**
