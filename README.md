Basic wepsite hosted using ansible in remote server 

STEP 1 - install ansible 
STEP 2 - create a inventory file  ex <invontory.ini
STEP 3 - create a yml file for playbook or clone from repo ex <Nginx.yml>
STEP 4 - run this command  - <ansible-playbook -i invontory.ini nginx.yml>

PLAYBOOK :->

---
- name: Install and Configure Nginx        # Playbook with a human-readable name, describing the purpose of the tasks inside.
  hosts: server                            # Specifies the target machine, `server`, which should be defined in the Ansible inventory file.
  become: true                             # Grants root (sudo) privileges, as many tasks require elevated permissions to execute.
  tasks:                                   # Starts the definition of tasks that will be executed sequentially.
    - name: Create a directory             # Task to ensure a directory exists on the server.
      file:                                # Uses the `file` module, which is responsible for managing files and directories.
        path: /var/www/mydirectory         # Specifies the directory path to create, ensuring `/var/www/mydirectory` is present.
        state: directory                   # Ensures the path is a directory (if it doesn’t exist, it will be created).
        mode: '0755'                       # Sets permissions for the directory: Owner has Read, Write, and Execute (rwx), while Group & Others have Read and Execute (r-x).

    - name: Install Nginx                   # Task to install the Nginx web server on the target machine.
      apt:                                 # Uses the `apt` module to manage packages on Debian-based systems.
        name: nginx                        # Specifies that the `nginx` package should be installed.
        state: present                      # Ensures the Nginx package is installed (if already installed, it will not be reinstalled).

    - name: Start Nginx Service             # Task to start the Nginx service after it has been installed.
      service:                             # Uses the `service` module to manage services on the target system.
        name: nginx                        # Specifies the name of the service (`nginx`) to start.
        state: started                      # Ensures that the `nginx` service is started (if already running, it remains active).

    - name: Download a zip file            # Task to download a ZIP file from a specified URL.
      get_url:                              # Uses the `get_url` module to download files from a URL to the target system.
        url: https://www.tooplate.com/zip-templates/2137_barista_cafe.zip  # Specifies the URL from which the ZIP file should be downloaded.
        dest: /var/www/mydirectory/2137_barista_cafe.zip  # Defines the destination path where the ZIP file will be saved.
        mode: '0644'                        # Sets permissions on the downloaded file: Owner can read and write (rw-), Group & Others can read (r--).

    - name: Unzip the downloaded file      # Task to extract the contents of the downloaded ZIP file.
      unarchive:                           # Uses the `unarchive` module to extract compressed files.
        src: /var/www/mydirectory/2137_barista_cafe.zip  # Specifies the path of the ZIP file to extract.
        dest: /var/www/mydirectory/        # Defines the destination directory where the ZIP file will be extracted.
        remote_src: yes                    # Indicates that the ZIP file is already located on the remote server, not a local file.

    - name: Copy extracted contents to /var/www/html   # Task to copy extracted files to the Nginx web root directory.
      copy:                                # Uses the `copy` module to copy files or directories.
        src: /var/www/mydirectory/2137_barista_cafe/   # Specifies the directory containing the extracted files.
        dest: /var/www/html/                # Specifies the destination directory where files will be copied (the web server's root).
        remote_src: yes                    # Indicates that the source files are located on the remote server.

