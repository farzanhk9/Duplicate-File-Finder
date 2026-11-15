import os0
import hashlib
from collections import defaultdict

def file_hash(path, chunk_size=8192):
    """Generate SHA-256 hash of a file."""
    sha256 = hashlib.sha256()
    try:
        with open(path, "rb") as f:
            while chunk := f.read(chunk_size):
                sha256.update(chunk)
        return sha256.hexdigest()
    except (IOError, PermissionError):
        return None

def find_duplicates(directory):
    hashes = defaultdict(list)

    for root, _, files in os.walk(directory):
        for name in files:
            file_path = os.path.join(root, name)
            hash_value = file_hash(file_path)
            if hash_value:
                hashes[hash_value].append(file_path)

    return {h: paths for h, paths in hashes.items() if len(paths) > 1}

if __name__ == "__main__":
    target_dir = input("Enter directory path: ").strip()
    duplicates = find_duplicates(target_dir)

    if not duplicates:
        print("No duplicate files found.")
    else:
        print("\nDuplicate files found:\n")
        for hash_value, files in duplicates.items():
            print(f"Hash: {hash_value}")
            for file in files:
                print(f"  - {file}")
            print()

