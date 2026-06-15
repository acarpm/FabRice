# Lancer Fabrice

```bash
cd ~/ros2_ws
  colcon build --packages-select fabrice_bringup
  source install/setup.bash
```

## Terminal 1 — CAN + bringup

```bash
sudo ip link set can1 down && sudo ip link set can1 up type can bitrate 1000000

source ~/ros2_ws/install/setup.bash

ros2 launch fabrice_bringup bringup.launch.py

```

## Terminal 2 — URDF pour Foxglove

```bash
ros2 run xacro xacro ~/ros2_ws/src/fabrice_bringup/description/urdf/fabric.urdf.xacro > /tmp/fabrice.urdf
python3 -m http.server 8080 --directory /tmp
```

## Terminal 3 — Téléopération (optionnel)

```bash
source ~/ros2_ws/install/setup.bash
ros2 topic pub /fabric_drive/cmd_vel geometry_msgs/msg/TwistStamped \
  "{header: {stamp: {sec: 0}}, twist: {linear: {x: 0.2}, angular: {z: 0.0}}}" -r 10
```

> Arrêter avec `Ctrl+C`. Commencer avec `x: 0.2` (m/s) pour tester doucement.

## Foxglove

1. Ouvrir **Foxglove Studio desktop** → connecter à `ws://192.168.2.5:8765`
2. Panneau 3D → Custom layers → **URDF** → Source: URL → `http://192.168.2.5:8080/fabrice.urdf`

## Notes

- L'interface CAN repasse à `DOWN` après un reboot — relancer la commande `ip link set` à chaque démarrage.
- Les ODrives doivent être alimentés **avant** de lancer le bringup.
- En cas d'erreur ODrive, vérifier avec `candump can1`.
