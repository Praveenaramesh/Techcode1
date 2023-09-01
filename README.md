# TXODDSTechtest
Producer consumer - Raw data conversion
"""
importing libraries
"""
import queue
import threading
import requests
import pdfplumber
"""
pdf opening 
"""
with pdfplumber.open("TXODDSPythonCodeTest_pdf") as pdf:
    for page in pdf.pages:
        for row in page.extract_tables():
            for cell in row:
                print(cell)

"""
create a queue
"""

queue = queue.Queue()


"""
create a producer thread
"""

class Producer(threading.Thread):

    def __init__ (self):
        super().__init__()

    def run(self):
        with pdfplumber.open ("TXODDSPythonCodeTest_pdf") as pdf:
            for page in pdf.pages:
                for url in page.extract_links():
                    queue.put(url)


"""
create a consumer thread
"""

class Consumer(threading.Thread):

    def __init__ (self):
        super().__init__()
    
    def run(self):
        while True:
            url = queue.get()
            response = requests.get(url)
            html = response.content
            links = []
            for link in html.findall("a"):
                links.append(link["href"])
            print(f"URL: {url} - Links: {links}")

"""
create a main thread
"""

def main():
   
    
    producer = Producer()
    consumer = Consumer()

    producer.start()
    consumer.start()

    producer.join()
    consumer.join()

main()

