import psutil
import time
import threading
from datetime import datetime
from pystray import Icon, Menu, MenuItem
from PIL import Image, ImageDraw

# ---------------MONITOR SETTINGS---------------
MONITORED_PROCESSES = ["python.exe", "firefox.exe", "spotify.exe", "discord.exe", "code.exe", "steam.exe"] # Change or add inside brackets to what you want to monitor
CHECK_INTERVAL = 20

# ---------------MONITORING LOGIC---------------
def find_processes_by_name(name):
    matches = []
    for proc in psutil.process_iter(['pid', 'name']):
        try:
            pname = proc.info['name']
        except (psutil.NoSuchProcess, psutil.AccessDenied):
            continue
        if pname and pname.lower() == name.lower():
            matches.append(proc)
    return matches

def monitor_processes():
    print(f"Monitoring processes: {MONITORED_PROCESSES}")

    while True:
        print(f"[{datetime.now().strftime('%H:%M:%S')}]")

        for name in MONITORED_PROCESSES:
            procs = find_processes_by_name(name)
            if not procs:
                print(f"{name} not running.")
            else:
                for proc in procs:
                    try:
                        if proc.is_running() and proc.status() != psutil.STATUS_ZOMBIE:
                            cpu = proc.cpu_percent(interval=0.1)
                            mem = proc.memory_info().rss / (1024 * 1024) # in MB
                            print(f"{name} (PID {proc.pid}) - CPU: {cpu:.1f}%| MEM: {mem:.1f} MB")
                        else:
                            print(f"{name} (PID {proc.pid}) - No longer running")
                    except (psutil.NoSuchProcess, psutil.AccessDenied):
                        print(f"{name} (PID {proc.pid}) - Access Denied or Process ended.")
            print("-" * 70)

        time.sleep(CHECK_INTERVAL)

# ---------------SYSTEM TRAY SETUP---------------
def create_icon_image():
    image = Image.new("RGB", (64, 64), "white")
    draw = ImageDraw.Draw(image)
    draw.ellipse((16, 16, 48, 48), fill="black")
    return image

def on_exit(icon, item):
    print("Shutting down monitor...")
    icon.stop()

def run_tray_app():
    icon = Icon(
        "ProcessMonitor",
        icon=create_icon_image(),
        title="Process Monitor",
        menu=Menu(MenuItem("Exit", on_exit))
    )

    threading.Thread(target=monitor_processes, daemon=True).start()

    icon.run()

# ---------------MAIN---------------
if __name__ == "__main__":
    run_tray_app()
