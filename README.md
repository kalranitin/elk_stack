### This is An Ansible playbook to setup a centralized logging server with logstash, redis, elasticsearch and kibana.

The box is based on the centralized best practive from logstash:
http://logstash.net/docs/1.4.2/tutorials/getting-started-centralized

http://elk/index.html#/dashboard/file/logstash.json

Basically the setup is:

-----------------------------------------------------------------
|                         The box                               |
-----------------------------------------------------------------
|  redis  -> logstash -> elasticsearch -> kibana                |
-----------------------------------------------------------------

To start the server run the playbook or take a look at my Vagrant box: [url]



### Testing and hello world

I recomment to take a look at my vagrant box which makes it very easy:

    $ git clone [url]
    $ cd logserver-vm
    $ vagrant up

Congratulations you now got a log server up and running.

To visit kibana go to: http://192.168.23.2/ and login, the default credentials are user: 'admin' an password: 'CHANGE ME!'



## Sending data to kibana

This is based on the logstash howto mentioned above:

Just setup logstash on your servers and point them to the redis server on your centralized logging server.

To simply test this, create a shipper.conf:

    # shipper.conf:
    input {
      stdin {
        type => "example"
      }
    }

    output {
      stdout { codec => rubydebug }
      redis { host => "192.168.23.2" data_type => "list" key => "logstash" }
    }

Now start logstash:

    $ java -jar logstash-1.3.3-flatjar.jar agent -f shipper.conf

You'll start a logstash process and can just type some messages in the stdin which will be sent to redis in the VM.

Next they'll be processed by logstash in the VM who will send them further to elasticsearch after which they can be
viewed in kibana.
