# avrotest

A simple example using Apache Avro to send structured objects 
-- Java objects, in this case -- through a message broker. The example
has two submodules, `sender` and `receiver`. You'll need to start
(and possibly obtain) an AMQP-compatible broker, then run the
sender and receiver as described below.

*This is not production-quality code*. It tries to demonstrate the simplest
possible way to use Avro in this setting, and has little regard for
error control or efficiency. There's a lot of duplication between the
sender and receiver, which is there to make each module self-contained.

The objects sent represent the properties of cartoon bears (because
why not?) The Avro schema is in `bear.avsc`. During compilation, a
Maven plug-in turns the schema into the source file of a Java class
-- `Bear.java` -- that can store objects with the appropriate properties.
This is not a necessary step with Avro -- you can use a different data
model, so long as it matches the schema.

To use a broker protocol other than AMQP, you'll need to change the
connection factory in both the sender and receiver, at the necessary
client runtime libraries as dependencies in pom.xml, and edit the
broker properties in `avrotest.props`.`

For a detailed description of how this application works, see:

https://kevinboone.me/avrotest.html

Copyright (c)2021 Kevin Boone, GPL v3.0

## Setting up and preparing the broker

To run this example without changes, you'll need a message broker with
AMQP support, such as Apache Artemis. You can download Artemis
from the project website:

https://activemq.apache.org/components/artemis/download/

then install and run it as follows:

    $ unzip /path/to/apache-artemis-xxx.zip
    $ cd apache-artemis
    $ ./bin/artemis create avrotest
    $ cd avrotest
    $ ./bin/artemis run

The default TCP port will be 61616.

To configure the code to use the broker, edit `avrotest.props` to modify the
connection URI if necessary, and set the user and password. The "queue\_name"
property sets the name of the broker queue to use for the test. This code
doesn't care what the queue name is but, of course, you don't want to use
a name that will class with anything else.

## Building

In the top-level directory:

    $ mvn package

This builds both the sender and the receiver

## Running the receiver

    $ cd receiver
    $ java -jar target/receiver-0.1-jar-with-dependencies.jar

Note that the reciever needs to be run from the `receiver` directory,
because it's hard-coded to read the Avro schema and the configuration
properties from the directory above it.

The receiver waits for messages, and decodes them one at a time to
instances of class Bear, before printing the results to standard out.

## Running the sender 

    $ cd sender 
    $ java -jar target/sender-0.1-jar-with-dependencies.jar

Note that the sender needs to be run from the `sender` directory,
because it's hard-coded to read the Avro schema and the configuration
properties from the directory above it.

The sender creates several instances of class Bear, serializes them
using Avro, then sends them to a queue on the message broker.


