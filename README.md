# cassandra [![Build Status](https://travis-ci.org/yglukhov/cassandra.svg?branch=master)](https://travis-ci.org/yglukhov/cassandra)
Nim bindings to cassandra db driver

# Usage
```nim
import asyncdispatch, cassandra

proc test() {.async.} =
    var host = getEnv("CASSANDRA_HOST")
    if host.len == 0: host = "127.0.0.1"

    let cluster = newCluster();
    let session = newSession();

    # Add contact points
    cluster.setContactPoints(host)

    # Provide the cluster object as configuration to connect the session */
    discard await session.connect(cluster)
    echo "Connected"

    # This operation will block until the result is ready */
    let statement = newStatement("SELECT * FROM system.schema_keyspaces WHERE keyspace_name = ?")
    statement[0] = "system"
    let res = await session.execute(statement)
    let val = res.firstRow.columns["strategy_class"]
    let cl = val.string
    echo "result: ", val
    assert(cl == "org.apache.cassandra.locator.LocalStrategy")

waitFor test()
```
