## Ollama Service 실행시 주의사항

로컬 네트워크 내의 다른 클라이언트에서 서버에 접근하기 위해서는 systemd 설정파일 수정을 해야함.

```bash
sudoedit /etc/systemd/system/ollama.service
```

서비스에서 OLLAMA_HOST 환경변수를 설정해야 외부에서 이용가능.

```bash
[Service]
Environment="OLLAMA_HOST=0.0.0.0:11434"
```

## Ollama Model 을 온라인 -> 오프라인 서버로 이관하는 법

오프라인 ollama 서버가 돌고 있다면 중지해줍니다.

```bash
sudo systemctl stop ollama
```

오프라인 ollama 서버에서 이용할 모델들을 네트워크가 연결된 ollama 서버에 pull 하여 저장합니다.

이 때 모델들은 `/usr/share/ollama/.ollama/models` 디렉토리에 저장됩니다.

해당 디렉토리 하위에는 모델의 weight 가 담겨있는 `blobs/` 디렉토리와 모델 메타데이터가 담겨있는 `manifests/` 디렉토리가 있습니다.

아래의 명령어로 온라인 서버에 다운로드한 모델들을 압축하여 저장합니다. 경우에 따라 `sudo` 로 실행해야 할 수도 있습니다.

```bash
tar -czvf ollama_models.tar.gz /usr/share/ollama/.ollama/models
```

압축된 파일을 오프라인 서버에 복사한 후, 아래 명령어로 압축을 풀어줍니다. 마찬가지로 경우에 따라 `sudo` 로 실행해야 할 수도 있습니다.

```bash
tar -xzvf ollama_models.tar.gz -C /usr/share/ollama/.ollama/models
```

만약 오프라인 서버에 이미 사용하는 모델들이 있다면 위처럼 복사하면 기존 모델들이 덮어씌워질 수 있습니다. 기존 모델을 유지해야한다면 아래의 명령어를 이용하세요.

```bash
tar -xzvf ollama_models.tar.gz -C /usr/share/ollama/.ollama/models --keep-old-files
```

