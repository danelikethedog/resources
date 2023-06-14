# E4K Testing

## Mappings

| ClientId | Chain |
| -------- | ----- |
| publisher   | 3 |
| publisher2  | 1 |
| subscriber0 | 3 |
| subscriber1 | 2 |
| subscriber2 | 1 |


| Topic | Chain |
| ----- | ----- |
| will1 | 3 |
| will2 | 1 |
| will5 | 2 |

## Scenario 2

Works on same chain but if its a cross chain it doesn't work

```bash
mosquitto_pub -t will -q 1 -r -V 5 -d -i "publisher" -h "127.0.0.1" -m 'hello' --repeat 5 --repeat-delay 1 --will-payload 'goodbye publisher' --will-qos 1 --will-topic 'will' -D WILL will-delay-interval 5 -D WILL message-expiry-interval 10 --will-retain -c

# then

mosquitto_pub -t will -q 1 -r -V 5 -d -i "publisher" -h "127.0.0.1" -m 'hello' --repeat 5 --repeat-delay 1 --will-payload 'goodbye publisher' --will-qos 1 --will-topic 'will' -D WILL will-delay-interval 5 -D WILL message-expiry-interval 10 --will-retain
```

## To Different Chain

```bash
# No will
mosquitto_pub -t will2 -q 1 -r -V 5 -d -i "publisher" -h "127.0.0.1" -m 'hello' --repeat 5 --repeat-delay 1

# No delay
mosquitto_pub -t will2 -q 1 -r -V 5 -d -i "publisher" -h "127.0.0.1" -m 'hello' --repeat 5 --repeat-delay 1 --will-payload 'goodbye publisher' --will-qos 1 --will-topic 'will2'

# Delay
mosquitto_pub -t will2 -q 1 -r -V 5 -d -i "publisher" -h "127.0.0.1" -m 'hello1' --repeat 5 --repeat-delay 1 --will-payload 'goodbye publisher' --will-qos 1 --will-topic 'will2' -D WILL will-delay-interval 5 -D WILL message-expiry-interval 10 --will-retain

# Delay with session expiry
mosquitto_pub -x 100 -t will2 -q 1 -r -V 5 -d -i "publisher" -h "127.0.0.1" -m 'hello1' --repeat 5 --repeat-delay 1 --will-payload 'goodbye publisher' --will-qos 1 --will-topic 'will2' -D WILL will-delay-interval 5 -D WILL message-expiry-interval 10 --will-retain
```

## To Same Chain

```bash
# No will
mosquitto_pub -t will1 -q 1 -r -V 5 -d -i "publisher" -h "127.0.0.1" -m 'hello' --repeat 5 --repeat-delay 1

# No delay will
mosquitto_pub -t will1 -q 1 -r -V 5 -d -i "publisher" -h "127.0.0.1" -m 'hello' --repeat 5 --repeat-delay 1 --will-payload 'goodbye publisher' --will-qos 1 --will-topic 'will1'

# Delay
mosquitto_pub -t will1 -q 1 -r -V 5 -d -i "publisher" -h "127.0.0.1" -m 'hello1' --repeat 5 --repeat-delay 1 --will-payload 'goodbye publisher' --will-qos 1 --will-topic 'will1' -D WILL will-delay-interval 5 -D WILL message-expiry-interval 10 --will-retain

# Delay with session expiry
mosquitto_pub -x 5 -t will1 -q 1 -r -V 5 -d -i "publisher" -h "127.0.0.1" -m 'hello1' --repeat 5 --repeat-delay 1 --will-payload 'goodbye publisher' --will-qos 1 --will-topic 'will1' -D WILL will-delay-interval 100 -D WILL message-expiry-interval 100 --will-retain
```

## Scenario 16

```bash
mosquitto_pub -t will2 -q 1 -r -V 5 -d -i "publisher" -h "127.0.0.1" -m 'hello' --repeat 5 --repeat-delay 1 -c

mosquitto_pub -t will2 -q 1 -r -V 5 -d -i "publisher" -h "127.0.0.1" -m 'hello1' --repeat 5 --repeat-delay 1 --will-payload 'goodbye publisher' --will-qos 1 --will-topic 'will2' -D WILL will-delay-interval 0 -D WILL message-expiry-interval 10 --will-retain
```
