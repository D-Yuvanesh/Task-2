Here’s an example of a multi-threaded file compression tool written in Python. It uses the threading module for concurrency and the zipfile module for compressing files.


---

Python Code: Multi-threaded File Compression Tool

import os
import zipfile
from threading import Thread
from queue import Queue

def compress_file(file_path, output_dir):
    """
    Compress a single file.
    """
    try:
        file_name = os.path.basename(file_path)
        zip_path = os.path.join(output_dir, f"{file_name}.zip")
        
        with zipfile.ZipFile(zip_path, 'w', zipfile.ZIP_DEFLATED) as zipf:
            zipf.write(file_path, arcname=file_name)
        
        print(f"Compressed: {file_path} -> {zip_path}")
    except Exception as e:
        print(f"Error compressing {file_path}: {e}")

def worker(queue, output_dir):
    """
    Thread worker to process files from the queue.
    """
    while not queue.empty():
        file_path = queue.get()
        compress_file(file_path, output_dir)
        queue.task_done()

def main():
    # Input directory with files to compress
    input_dir = input("Enter the directory containing files to compress: ").strip()
    output_dir = input("Enter the output directory for compressed files: ").strip()
    
    # Validate directories
    if not os.path.isdir(input_dir):
        print("Invalid input directory!")
        return
    if not os.path.exists(output_dir):
        os.makedirs(output_dir)
    
    # List all files in the input directory
    files = [os.path.join(input_dir, f) for f in os.listdir(input_dir) if os.path.isfile(os.path.join(input_dir, f))]
    
    # Create a queue and add files to it
    file_queue = Queue()
    for file in files:
        file_queue.put(file)
    
    # Number of threads to use
    num_threads = min(4, len(files))  # Adjust thread count based on the number of files
    print(f"Starting compression with {num_threads} threads...")
    
    # Start worker threads
    threads = []
    for _ in range(num_threads):
        thread = Thread(target=worker, args=(file_queue, output_dir))
        thread.start()
        threads.append(thread)
    
    # Wait for all threads to finish
    for thread in threads:
        thread.join()
    
    print("Compression completed!")

if __name__ == "__main__":
    main()


---

Features:

1. Multi-threading: Multiple files are compressed simultaneously to improve performance.


2. Queue: Ensures thread-safe handling of files.


3. Customizable Input/Output: Allows specifying input and output directories.


4. Error Handling: Reports issues during compression.




---

Usage:

1. Save this script as multi_threaded_compressor.py.


2. Run it in the terminal:

python multi_threaded_compressor.py


3. Enter:

The path to the directory containing files to compress.

The path to the directory where the compressed files should be stored.



4. The program will create .zip files for each file in the input directory.




---

Example Execution:

1. Input directory: /path/to/input_files/


2. Output directory: /path/to/output_zips/


3. Output for each file:

Compressed: /path/to/input_files/file1.txt -> /path/to/output_zips/file1.txt.zip




---

Let me know if you need additional features like compressing directories or progress tracking!

