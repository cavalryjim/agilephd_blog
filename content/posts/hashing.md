+++
title = 'Hashing'
tags = ["blockchain", "python"]
date = 2024-03-21
+++

This post will discuss the concept of hashing and provide examples using the Python programming language.

### The Hash

A hash refers to a mathematical function that takes some type of information as input and returns an encrypted output with a fixed length.  This output, or hash, is unique to the information used as the input.  You can think of it as being like a fingerprint.

[SHA256](https://docs.python.org/3/library/hashlib.html) is a popular cryptographic hash algorithm that produces a 256-bit hash value. It is commonly used to check the integrity of files and verify the authenticity of files or data. It is also widely used in the cryptocurrency world to secure transactions on the blockchain. In this post, we’ll look at how to generate and use SHA256 hashes in Python.

### What is a SHA256 Hash?

A SHA256 hash is a cryptographic hash algorithm that takes an input of any length and produces a fixed-size 256-bit (32-byte) hash value. The output is known as a hash digest and is used as a one-way function to verify the integrity of a file.

The SHA256 algorithm is designed to be collision-resistant, meaning it is very difficult to find two different inputs that will produce the same output. This makes it useful for verifying the authenticity of a file or data.

### Generating a SHA256 Hash in Python

Python makes it simple to generate a SHA256 hash. The `hashlib` package, which is included in the Anaconda distribution of Python, and perhaps most others, provides several hashing algorithms including SHA256.

To generate a SHA256 hash, we can use the `hashlib.sha256()` function. This function takes a string as an argument and returns the SHA256 hash of the input string.

Here’s a simple example of how to generate a SHA256 hash in Python:

```python
import hashlib

# Input string
input_string = 'hello world'

# Generate SHA256 hash
sha256_hash = hashlib.sha256(input_string.encode('utf-8')).hexdigest()

# Print the hash
print(sha256_hash)
```
```
b94d27b9934d3e08a52e52d7da7dabfac484efe37a5380ee9088f7ace2efcde9
```


If you run the above code, you should get the exact same hash output.  Also try modifying the `input_string` and rehashing.  You should notice that the output changes rather significantly.  Even if all you do is add or remove a single space to the input, it is easy to notice the change.  This drastic change is known as the [Avalance Effect](https://en.wikipedia.org/wiki/Avalanche_effect).

Also notice the length of the hash, 64 hexidecimal characters.

```python
# what is the length of this hash?
len(sha256_hash)
# returns 64
```

The `hashlib.sha256()` function also accepts a bytes-like object as an argument, so we can use it to generate the SHA256 hash of a file. Here’s an example of how to do this with a file called `text_file.txt` containing everyones favorite, lorem ipsum:

```
Lorem ipsum dolor sit amet, consectetur adipiscing elit.
Vivamus condimentum sagittis lacus, laoreet luctus ligula laoreet ut.
Vestibulum ullamcorper accumsan velit vel vehicula.
Proin tempor lacus arcu.
Nunc at elit condimentum, semper nisi et, condimentum mi.
In venenatis blandit nibh at sollicitudin.
Vestibulum dapibus mauris at orci maximus pellentesque.
Nullam id elementum ipsum. Suspendisse cursus lobortis viverra.
Proin et erat at mauris tincidunt porttitor vitae ac dui.
```

```python
# Open the file
with open("text_file.txt", "rb") as f:
    # Read the contents of the file
    data = f.read()

# Generate SHA256 hash
sha256_hash = hashlib.sha256(data).hexdigest()

# Print the hash
print(sha256_hash)
```
```
d16267b56ea53c1b340a3fd9ba7f70d8a6037abf350b492b60264ff32f19faf0
```

Once again, I would encourage you to try modifying the contents of the text file and rehashing it to observe the Avalance Effect.  Notice that if you have the hash of a file, you can use that to confirm the integrity of the content.


### Using a SHA256 Hash to Verify File Integrity

Once we have the SHA256 hash of a file, we can use it to verify the integrity of a file or other data. This is useful for verifying the authenticity of downloaded files or blocks of data.

We can do this by comparing the SHA256 hash of the downloaded file with the expected hash. If the hashes match, then we can be sure that the file has not been tampered with.

Here’s an example of how to do this in Python:

```python
# Expected hash
confirmation_hash = "d16267b56ea53c1b340a3fd9ba7f70d8a6037abf350b492b60264ff32f19faf0"

# Open the file
with open("text_file.txt", "rb") as f:
    # Read the contents of the file
    data = f.read()

# Generate SHA256 hash
sha256_hash = hashlib.sha256(data).hexdigest()

# Compare hashes
if sha256_hash == confirmation_hash:
    print("Hashes match! The file is authentic.")
else:
    print("Hashes do not match! The file may have been tampered with.")
```
```
Hashes match! The file is authentic.
```

In this example, the file is authentic!  Conversely, if the file has been tampered with, we would know because it results in a different hash.  

### Large Files

Notice that, thus far, the size of the hash remains the same regardless of the size of the item to be hashed.  Let's hash an image file.

![Mike strolling around](../../mike.png)

```python
# Open the file
with open("mike.png", "rb") as f:
    # Read the contents of the file
    data = f.read()
    
# Generate SHA256 hash
sha256_hash = hashlib.sha256(data).hexdigest()

# Print the hash
print(sha256_hash)
```
```
74a3d70ac1fe79985c58e76684ea72d69df1024a087f8e0750f8f800df1030ce
```

How about a video file?

https://agilephd.s3.amazonaws.com/Afghanistan_homecoming.mov

```python
# Open the file
with open("Afghanistan_homecoming.mov", "rb") as f:
    # Read the contents of the file
    data = f.read()
    
# Generate SHA256 hash
sha256_hash = hashlib.sha256(data).hexdigest()

# Print the hash
print(sha256_hash)
```
```
9b6df1e46424704e281ea1b9e933fec9cc599e9f5f75ddba2bf1deed647e6b2d
```

 As we moved from a simple "hello world" string to text files, images, and video files the size of the hash remained consistent at 64 hexidecimal characters.

```python
len(sha256_hash)
# returns 64
```

### Conclusion

In these examples, we looked at how to generate and use SHA256 hashes in Python. We saw how to generate a SHA256 hash of a string or a file and how to use it to verify the integrity of a file.

One of the major takeaways should be that hashing is a powerful mechanism for digitally signing data, such as information in a single block of a Blockchain, that validates the integrity of the data.





