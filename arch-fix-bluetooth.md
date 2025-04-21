---
created: 2025-04-21
url: https://unix.stackexchange.com/questions/700030/no-default-controller-available-bluetooth-arch-linux
---

# arch fix bluetooth

## content

### error

```bash
No default controller available Bluetooth
```

### check

```bash
sudo lsmod | grep blue
```

### fix

```bash
rmmod btusb
modprobe btusb
```

## refs

### up

[[arch]]

### down

- https://unix.stackexchange.com/questions/700030/no-default-controller-available-bluetooth-arch-linux