# FISH-HASH-CLIENT

# Docker Image Integrity Verification

Docker does not perform image integrity verification at runtime.  

**What if?**  

What if an attacker gains access to the host, silently modifies the Docker overlay2 file system, injects malicious code, and exfiltrates internal data?  

- CI/CD pipelines scan images, but only at build time, making it difficult to detect post-build modifications.  
- Runtime security solutions focus on detecting live filesystem modifications and anomalies. If a tampered image runs, it may go unnoticed.  
- Security teams would need to manually inspect network anomalies to detect such attacks.  

## Solution  

1. When an image is initially pulled (`docker pull`), store the hash of the overlay2 layer.  
2. Before starting a container, calculate the hash of the overlay2 layer and compare it with the stored hash.  
3. If the hash values differ or no stored hash exists, the image is considered compromised.  
   - A different hash value indicates the overlay2 layer was modified.  
   - A missing stored hash suggests the image was not pulled through a trusted source.  
4. If a hash mismatch is detected, delete the container and trigger an alert.

&nbsp;

> This ensures Docker image integrity verification at runtime.

&nbsp;

# 도커 이미지 무결성 검증

Docker는 기본적으로 실행 시 이미지 무결성 검증을 하지 않는다.  

**만약에?**  

공격자가 호스트에 침입해서 조용히 도커 overlay2를 변조해서 악성코드를 심어서 내부 데이터를 외부로 전송한다면 어떻게 될까?  

- CI/CD 파이프라인에서 이미지를 스캔하지만, 이는 빌드 시점에 한정되어 이미지가 변조된 사실을 알아차리기 힘들다.
- 런타임 보안 솔루션은 실행 중인 파일 시스템 변조나 이상 행위를 감지하는 데 집중하기 때문에, 변조된 이미지가 실행되면 런타임 보안 솔루션은 이를 알아차리기 힘들다.
- 보안 담당자가 직접 네트워크 이상 징후를 찾아야 탐지 가능하다.

## 해결책

1. 최초 `docker pull` 시 overlay2 레이어의 해쉬 값을 저장한다.
2. 컨테이너 시작 시 overlay2 레이어의 해쉬 값을 계산하여 저장된 해쉬값과 비교한다.
3. 해쉬값이 다른 경우 또는 저장된 해쉬값이 없는 컨테이너 이미지는 악성이다.
   - 해쉬값이 변조되었다면 = overlay2 레이어 변조의 결과이다.
   - 저장된 해쉬값이 없다면 = 정상적으로 `docker pull`이 이뤄지지 않았다.
4. 해쉬 값에 문제가 있다면 컨테이너를 삭제하고, 경고를 보낸다.

&nbsp;

> 이를 통해 도커 이미지의 무결성을 실행 시마다 확인할 수 있다.
