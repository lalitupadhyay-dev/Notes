# Stateful Authentication

- Token is shared to <span style="color: rgb(255, 204, 0)">**User**</span> and also stored in the <span style="color: rgb(255, 41, 41)">**Redis Store**</span> or any other in-memory cache and not in a <span style="color: rgb(13, 110, 255)">**Database**</span>, because it provides faster reads than a *DB*.

- **Pros:**
    - High Security
    - Server has control to force logout the user if any shady (malicious) things happen
    
- **Cons:**
    - Hard to scale (if multiple servers)
    - High memory usage

- **Usage:**
    - Banking apps
    - Financial apps in companies

- **Flow of Stateful Authentication**

    <img src="./imgs/2.png" />