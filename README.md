**Full-Stack AI Chat Application (AWS Deployment)**


A Full-stack GenAI application that hosts a local LLM (Large Language Model) on the cloud. The project decouples the frontend and backend for maximum efficiency: a static React UI hosted on AWS S3 communicates with a Python FastAPI inference engine running on AWS EC2, powered by Ollama.


**Architecture** 


•	**Frontend:** React.js (hosted on AWS S3 Static Website Hosting)


•	**Backend:** FastAPI + Python (hosted on AWS EC2 Ubuntu Instance)


•	**AI Engine:** Ollama (running qwen2.5:0.5b)


•	**Infrastructure:** AWS (EC2 for compute, S3 for storage)


•	**Process Management:** Systemd (Linux) for backend persistence.
________________________________________
**Deployment Guide**


**Phase 1: The Backend (AWS EC2)**


The "Brain" of the application runs on an AWS EC2 instance (Ubuntu).


**1. Infrastructure Setup**


    •	Launched an Ubuntu EC2 instance.


    •	Configured Security Group to allow traffic on Port 8000 (API) and 22 (SSH).


    •	Allocated an Elastic IP to prevent IP changes on restart.


**2. AI Model Setup** 


Installed Ollama and pulled a lightweight, high-performance model optimized for the instance.


Go to Bash-->> curl -fsSL https://ollama.com/install.sh | sh
          -->> ollama pull qwen2.5:0.5b



**3. API Implementation (FastAPI)**
Created a Python backend to bridge the frontend and the AI model.


•	**Dependencies:** fastapi, uvicorn, httpx
•	**CORS Configuration:** Enabled CORSMiddleware to allow requests from the S3 bucket URL.


**4. Production Service (Systemd)**
Configured the backend to run as a background service, ensuring it auto-restarts if the server reboots.


        -->> File: /etc/systemd/system/ollama-chat-api.service
        -->> Status Check: sudo systemctl status ollama-chat-api
________________________________________
**Phase 2: The Frontend (AWS S3)**


The "Face" of the application is a high-performance React SPA (Single Page Application).


**1. Configuration**


        Updated App.js to point to the EC2 Elastic IP:
**JavaScript**


        const API_BASE_URL = 'http://<YOUR_ELASTIC_IP>:8000';


**2. Building for Production**


        Compiled the React code into static HTML/CSS/JS files:


        Go to Bash->> npm run build


**3. S3 Deployment**


    •	Created an S3 bucket (ollama-chat-frontend-rampally).


    •	Enabled Static Website Hosting.


    •	Configured Bucket Policy to allow public read access

________________________________________
**🛠️ Management & Controls**


**How to Connect (SSH)**


-> To access the backend server for maintenance:


        --> Go to Bash -->> ssh -i "path/to/key.pem" ubuntu@<YOUR_ELASTIC_IP>



**Essential Maintenance Commands**


Once inside the server, use these commands to control the AI backend:


**Check Backend Status:** sudo systemctl status ollama-chat-api


**View Live Logs:** 	sudo journalctl -u ollama-chat-api -f


**Restart Backend:** 	sudo systemctl restart ollama-chat-api


**Check Installed Models:**	ollama list


**Install New Model:**	ollama pull <model_name>


**Updating the Frontend**
1.	Make changes in VS Code.
2.	Run npm run build.
3.	Upload the new contents of the build folder to the S3 bucket.
________________________________________
**Key Challenges Solved during Engineering**


**1.	CORS Errors:** Configured FastAPI middleware to accept cross-origin requests from the S3 domain.


**2.	Process Management:** Migrated from manual execution (python main.py) to a robust systemd service configuration to ensure 24/7 uptime.


**3.	Environment Management:** Resolved 203/EXEC errors by correctly configuring Python Virtual Environments (venv) within the production folder.


**4.	Model Optimization:** Switched from llama2 to qwen2.5:0.5b to optimize response latency on the available hardware. 

