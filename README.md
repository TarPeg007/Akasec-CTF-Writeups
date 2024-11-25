Diving into Akasec CTF 2024: A Reverse Engineering Adventure
Introduction
The Akasec CTF 2024 presented an exciting challenge for reverse engineers, offering a series of intricate binary puzzles that tested skills in understanding and manipulating low-level code. This writeup explores the journey through four unique challenges, each presenting its own set of complexities and learning opportunities.
Challenge Breakdown
1. Sperm Rev: The Deceptively Simple Challenge
Difficulty Level: Easy
Key Technique: Strings Analysis
The first challenge taught a critical lesson in reverse engineering: always start with the simplest tools. Many participants, including myself, initially overcomplicated the approach by diving into complex disassembly techniques.
Solving Strategy:

Use the strings command
Extract readable strings from the binary
Identify the flag quickly and efficiently

2. Paranoia: Navigating Randomness
Difficulty Level: Moderate
Key Techniques:

Time-based Random Number Generation
XOR Decryption
Seed Replication

Technical Deep Dive
The challenge leveraged classic C functions to obfuscate the flag:

time(): Returns seconds since the Unix epoch
srand(): Seeds the random number generator
rand(): Generates pseudo-random numbers

Solution Approach:
pythonCopydef solve_paranoia():
    # Replicate exact timestamp
    libc.srand(current_timestamp)
    
    # Reverse XOR encryption
    flag = [chr(rand_value ^ encrypted_char) 
            for encrypted_char in encrypted_flag]
    return ''.join(flag)
3. Grip: Shellcode Decryption Challenge
Difficulty Level: Intermediate
Key Techniques:

Shellcode Analysis
XOR Decryption
Memory Mapping

Solving Methodology:

Extract encrypted shellcode
Use XOR decryption (key: 0xf2)
Analyze memory mapping with mmap()
Utilize CyberChef for decryption

4. Paketa: The Advanced Packing Challenge
Difficulty Level: Advanced
Key Techniques:

Custom Packing Mechanism
RC4 Encryption
ELF Binary Reverse Engineering

Packing Mechanism Breakdown

Encryption Algorithm: RC4
Key Generation: Custom algorithm
Encryption Scope: ELF Binary Sections

Decryption Strategy:
pythonCopydef unpack_elf(packed_binary):
    for potential_seed in range(1, 501):
        key = generate_key_stream(seed=potential_seed)
        decrypted_sections = decrypt_sections(packed_binary, key)
        if validate_decryption(decrypted_sections):
            return decrypted_sections
Key Learning Insights

Start with Simple Tools

strings command can reveal quick wins
Don't immediately jump to complex analysis


Understand Fundamental Techniques

Random number generation
Encryption mechanisms
Memory manipulation


Toolset Mastery

Binary Ninja
Ghidra
CyberChef
Pwntools



Conclusion
The Akasec CTF 2024 was more than a competition—it was a masterclass in reverse engineering. Each challenge represented a unique puzzle, testing not just technical skills but creative problem-solving.
Final Takeaway: Reverse engineering is an art of patience, creativity, and persistent curiosity.
Tools Used

Binary Ninja
Ghidra
CyberChef
Pwntools
Python
Linux command-line tools

Acknowledgments
Special thanks to the Akasec CTF team for creating such compelling challenges that push the boundaries of reverse engineering skills.