---
created: 2025-04-21
url: https://unix.stackexchange.com/questions/700030/no-default-controller-available-bluetooth-arch-linux
---

# arch fix bluetooth

## content

### error: no def ctrl

```bash
No default controller available Bluetooth
```

#### check

```bash
sudo lsmod | grep blue
```

#### fix

```bash
rmmod btusb
modprobe btusb
```

short

```bash
sudo rmmod btusb && sudo modprobe btusb
```

### error: io

```bash
Failed to connect: org.bluez.Error.Failed Input/output error
```

#### check

```bash
rfkill list
```

#### fix

```bash
rfkill list
```

## refs

### up

[[arch]]

### down

- https://unix.stackexchange.com/questions/700030/no-default-controller-available-bluetooth-arch-linux