// Licensed to the Apache Software Foundation (ASF) under one
// or more contributor license agreements.  See the NOTICE file
// distributed with this work for additional information
// regarding copyright ownership.  The ASF licenses this file
// to you under the Apache License, Version 2.0 (the
// "License"); you may not use this file except in compliance
// with the License.  You may obtain a copy of the License at
//
//   http://www.apache.org/licenses/LICENSE-2.0
//
// Unless required by applicable law or agreed to in writing,
// software distributed under the License is distributed on an
// "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
// KIND, either express or implied.  See the License for the
// specific language governing permissions and limitations
// under the License.

= Half-closed example

Skupper-router instantiates a TCP listener on port 8000 for incoming
client connections. It also instantiates a TCP connector to connect to
a server at 127.0.0.1:8800

The client-half-close client will connect to skupper router port
8000. skupper-router will then connect to the server-half-close at
127.0.0.1:8800. The client will then send data and close its write-end
of its socket (half-close). skupper-router will forward the data, then
the FIN for the half-close to the server. The server will read the
data, detect the half-close, then write a response. After writing the
response, the server will close its socket
(full-close). skupper-router will forward the data to the client,
which then reads it. skupper-router will also forward the FIN from the
server. The client will detect the FIN (close) coming from
skupper-router and then close its socket and exit.

How to run the example:

Optional prerequisite: run tcpdump in the background to monitor the server and client sockets. Example:

$ sudo tcpdump -nn -i lo 'tcp port 8000 or tcp port 8800' &

Run skupper-router in the background passing the configuration file via the -c option:

$ skrouterd -c ./skrouterd.conf &

Spawn the server to the background as well:

$ ./server-half-close &

Then run the client:

$ ./client-half-close

You will see the TCP trace generated by tcpdump as the test runs. The
client will pause between steps to make it easier to identify what is
going on "on the wire" as the test runs.

At the end the client-half-close will exit. You can then kill the
skupper-router and server-half-close processes.
