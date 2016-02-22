Sharing File Descriptors Between Unrelated Processes
---
The daemon process:
```ruby
require 'socket'

# This sockets listens for local connections.
listener = UNIXServer.new('/tmp/pingback.sock')
client = listener.accept

loop do
  # This is where an open connection is received.
  connection = client.recv_io

  # An exciting response. Echo!
  connection.write(connection.read)
  connection.close
end
```
The client process:
```ruby
require 'socket'

# This socket listens for connections from the outside world.
listener = TCPServer.new('0.0.0.0', 4481)

# This socket connects up to the local daemon.
receiver = UNIXSocket.new('/tmp/pingback.sock')

Socket.accept_loop(listener) do |connection, _|
  # When it gets a new connection it passes it right to the
  # daemon process.
  receiver.send_io(connection)

  # Passing the connection to the daemon made a copy of it,
  # so now there are two open connections. If we didn't close
  # it here then the connection would remain open even after
  # it was closed by the daemon.
  connection.close
end
```
