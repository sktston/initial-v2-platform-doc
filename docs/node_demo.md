# VCX NodeJS Wrapper

<https://github.com/sktston/vcx-demo-node>

This is a NodeJS wrapper for VCX library.
VCX is the open-source library on top of Libindy which fully implements the credentials exchange.

**Note**: This library is currently in experimental state.

## Contribution Guide

Make sure you have these packages installed:

* StandardJS
* Typescript
* TSLint


Also this has a dependency on:
* libvcx debian
Because it creates a symlink (/usr/lib/libvcx.so)

Run this commands before submitting your PR:

```
npm run lint
```

## Documentation:
 Run these commands:
```
npm install
npm ci
npm run doc-gen
```
* A directory will be created locally `./docs` which contains an `index.html` file which can be used to navigate the
generated documents.

## Run Demo
- The demo represents example how 2 actors, **Alice** and **Faber** institution, exchange credentials.
- They may consult Indy blockchain (pool of Indy nodes)  to find out certain pieces of information. **Faber**
and **Alice** are represented by 2 scripts `faber.js` and `alice.js` but you could imagine that there's a webserver
running code alike what's inside `faber.js` and there's a perhaps smartphone or laptop running code
alike in `alice.js`.
- **Faber** and **Alice** in the demo also don't exchange the credentials peer to peer. Instead, the exchange happens
through intermediary service represented by **Dummy Cloud Agent**. The data **Alice** and **Faber** are exchanging over
**Dummy Cloud Agent** are however encrypted and cannot be read by the **Dummy Cloud Agent**. The **Dummy Cloud Agent**
is something like illiterate postman. He'll take a letter from one party and delivers it to the other party. But he's
unable to read the messages he's handling.

### Pre-requirements
##### Libraries
Before you'll be able to run demo, you need to make sure you've compiled
- [`libindy`](https://github.com/hyperledger/indy-sdk/tree/master/libindy)
- [`libvcx`](https://github.com/hyperledger/indy-sdk/tree/master/vcx)
- [`libnullpay`](https://github.com/hyperledger/indy-sdk/tree/master/libnullpay)
- Optionally [`libindystrgpostgres`](https://github.com/hyperledger/indy-sdk/tree/master/experimental/plugins/postgres_storage) if you want to run demo
with postgres wallet.

Library binaries must be located `/usr/local/lib` on OSX, `/usr/lib` on Linux.

#### Indy pool
You'll also have to run pool of Indy nodes on your machine. You can achieve by simply running a docker container
which encapsulates multiple interconnected Indy nodes.
[Instructions here](https://github.com/hyperledger/indy-sdk#how-to-start-local-nodes-pool-with-docker).

### Steps to run demo
- Install NodeJS dependencies
```
npm install
```

- Compile LibVCX Wrapper
```
npm run compile
```
- Start [Dummy Cloud Agent](../../dummy-cloud-agent)
- Run Faber agent, representing an institution
```
npm run demo:faber
```
- Give it a few seconds, then run Alice's agent which will connect with Faber's agent
```
npm run demo:alice
```

### Demo with Posgres wallet
You can also run demo in mode where both Faber and Alice are using Postgres wallets. Follow
[instructions](https://github.com/hyperledger/indy-sdk/tree/master/experimental/plugins/postgres_storage) to
compile postgres wallet plugin and startup local postgres docker container.

Once yu have that ready, use these commands to start demo in postgres mode.
```
npm run demo:faber:pg
```
```
npm run demo:alice:pg
```

### Demo with webhook notifications
Another feature are webhook notifications. If you run sample webhook notification server by running:
`npm run demo:notifyserver`

This will start new http server on `localhost:7209`.

Opened port on `localhost:7209` will modify `Faber` and `Alice` in the demo in following way:
They will register their notification webhook in cloud agency. Everytime cloud agent will receive
a message on behalf of the owner, it will send notification to webhook url specified in agent configuration.
In the case of this demo, it's by default `localhost:7209/notifications/faber` and
`localhost:7209/notifications/alice`.

# Additional Demo

Examples of multi-holder implementation by extending the previous `faber-alice example` are in the `demo-multi` and `demo-webhook` directories. In the previous faber-alice example, Faber is implemented to act as the Issuer and Verifier at the same time. In the multi-holder examples, this is divided into Issuer and Verifier respectively. (faber-issuer, faber-verifier)

Both the `demo-multi` and` demo-webhook` examples implement multi-holders, but the difference between the agent message check method is that demo-multi uses the polling method and demo-webhook uses the webhook triggered method.

Before running `demo-multi` and `demo-webhook`, [`dummy cloud agent`](https://github.com/hyperledger/indy-sdk/tree/master/vcx/dummy-cloud-agent) or [`vcxnodeagency`](https://github.com/AbsaOSS/vcxagencynode) must be executed first. It is recommended to use 3 terminals for example execution
```
Terminal-1: ~/demo-webhook/node faber-issuer.js -> copy invitation code
Terminal-2: ~/demo-webhook/node faber-verifier.js -> copy invitation code
Terminal-3: ~/demo-webhook/node alice-multi.js -i 'issuer invitation code' -v 'verifier invitation code' -n <number of alice>
```

- alice-multi.js must provide the corresponding invitation code as a parameter to connect to the faber-issuer and faber-verifier. 
  - `-i` and `-v` options
  - Be careful to wrap it with **' '** when giving invitation code arguments to the shell. for example, **node alice-multi.js -v '{"@id":"70d5ed' -v '{"@id":"3a685538-78' -n 16**
- In the `demo-multi` example, the invitation code must be given through the -i and -v options, but can be omitted in the `demo-webhook` example. 
  - In the demo-webhook example, the http server is running on the faber-issuer and faber-verifier, and alice-multi can connect to the server to receive the invitation code.
  - For example, **node alice-multi.js -n 16**

