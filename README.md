# stream_downloader
```python 3
from tqdm import tqdm

def download_manual(session: requests.Session, url: str, filename: str) -> None:
    video = session.get(url, allow_redirects=True, stream=True)
    video_length = int(video.headers['Content-Length'])
    cur = 0
    with open(filename, 'wb') as f:
        with tqdm(total=video_length, unit='B',
                  unit_scale=True, desc=filename, initial=cur, ascii=True) as pbar:
            while cur < video_length:
                resume_header = {
                    "Range": f"bytes={cur}-"
                }
                video = session.get(url, stream=True, headers=resume_header)
                for chunk in video.iter_content(chunk_size=1024 * 1024):
                    if chunk:
                        f.write(chunk)
                        pbar.update(len(chunk))
                cur = Path(filename).stat().st_size

```


Остается в основном коде взять 

```
video = session.get(url, allow_redirects=True, stream=True)
video_length = int(video.headers['Content-Length'])
my_file_len = Path(filename).stat().st_size
if video_length > my_file_len:
    стираем кривой файл
    download_manual(session, url, filename)

```
