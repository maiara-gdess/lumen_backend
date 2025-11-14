# Lumen - Documentação de Execução

## Requisitos
- Python 3.10+
- Windows/PowerShell (comandos abaixo estão em PowerShell)
- Internet para síntese de fala (gTTS)
- Webcam para captura (OpenCV)

## Estrutura do projeto
```
api_gateway.py                 # API FastAPI (Uvicorn)
dashboard/app.py               # Dashboard Flask
services/                      # Serviços independentes
  camera_service.py            # Captura imagem da webcam (CLI)
  recognition_service.py       # Reconhecimento (simulado) (CLI)
  speech_service.py            # Síntese de fala (CLI)
  history_service.py           # Persistência em SQLite (CLI)
database/lumen.db              # Banco SQLite
requirements.txt               # Dependências
```

## Setup (primeira vez)
```powershell
cd C:\Users\maiar\OneDrive\Desktop\lumen
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```
Se a ativação for bloqueada:
```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
.\.venv\Scripts\Activate.ps1
```

## Executar a API (FastAPI/Uvicorn)
```powershell
cd C:\Users\maiar\OneDrive\Desktop\lumen
.\.venv\Scripts\Activate.ps1
python -m uvicorn api_gateway:app --host 127.0.0.1 --port 8000 --reload
```
- Docs interativas: http://127.0.0.1:8000/docs
- Endpoint principal: `POST /recognize/` (envie um arquivo de imagem)

Exemplo de teste via PowerShell (Invoke-RestMethod):
```powershell
$File = Get-Item .\exemplo.jpg
Invoke-RestMethod -Uri http://127.0.0.1:8000/recognize/ -Method Post -Form @{ file = $File }
```

## Executar o Dashboard (Flask)
Em outro terminal/aba:
```powershell
cd C:\Users\maiar\OneDrive\Desktop\lumen
.\.venv\Scripts\Activate.ps1
python dashboard\app.py
```
- Acesse: http://127.0.0.1:5000

## Serviços (executáveis isolados em `services/`)
Ative o venv antes em cada terminal:
```powershell
cd C:\Users\maiar\OneDrive\Desktop\lumen
.\.venv\Scripts\Activate.ps1
```

### CameraService
Captura uma foto da webcam para arquivo.
```powershell
python services\camera_service.py --out captured.jpg
```
Saída esperada: `Imagem capturada em: captured.jpg`

### RecognitionService
Reconhece um rótulo (simulado) para uma imagem.
```powershell
python services\recognition_service.py caminho\da\imagem.jpg
```
Saída: `Objeto reconhecido: Garrafa` (ou outro rótulo aleatório)

### SpeechService
Converte texto em fala usando gTTS e toca o MP3 no Windows.
```powershell
python services\speech_service.py "Olá do Lumen" --out output.mp3
```
Saída: `Áudio salvo em: output.mp3`

### HistoryService
Salvar um objeto no histórico:
```powershell
python services\history_service.py save --object Garrafa
```
Listar o histórico:
```powershell
python services\history_service.py list
```

## Dicas e Solução de Problemas
- Uvicorn: `ERROR: Import string must be in format <module>:<attribute>`
  - Rode o comando a partir da pasta do projeto e use `api_gateway:app`.
  - Confirme que o arquivo se chama `api_gateway.py` e contém `app = FastAPI()`.

- `ModuleNotFoundError: No module named 'gtts'` (ou outros pacotes)
  - Ative o venv: `.\.venv\Scripts\Activate.ps1`
  - Reinstale: `pip install -r requirements.txt`
  - Verifique o Python/Pip em uso: `where python`, `where pip` (devem apontar para `.venv`).

- Portas ocupadas (8000/5000)
  - Altere a porta: `--port 8001` (por exemplo)
  - Ou finalize o processo que está usando a porta.

- Webcam não funciona (OpenCV)
  - Verifique permissões do sistema e se outra aplicação está usando a câmera.
  - Troque o índice da câmera no código se necessário (`cv2.VideoCapture(1)`, etc.).

- gTTS sem áudio
  - Requer internet para sintetizar.
  - No Windows, usamos `start output.mp3`. Garanta um player associado a `.mp3`.

## Testes
Há arquivos de teste em `tests/` como placeholders. Você pode criar testes com `pytest` se desejar:
```powershell
pip install pytest
pytest
```

---
Qualquer dúvida adicional sobre execução ou erros específicos, descreva o erro apresentado no terminal.
