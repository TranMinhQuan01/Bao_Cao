**HISTOGRAM EQUALIZATION**  

**GIỚI THIỆU**  

Histogram Equalization là một phương pháp xử lý ảnh để cải thiện độ tương phản bằng cách “kéo dãn” phân bố giá trị pixel trên histogram. Phương pháp này giúp tăng cường độ tương phản toàn cục của hình ảnh, đặc biệt là khi hình ảnh có phạm vi giá trị cường độ hẹp. Điều này cho phép các khu vực có độ tương phản cục bộ thấp có được độ tương phản cao hơn. Tuy nhiên, một nhược điểm là nó có thể tăng độ tương phản của nhiễu nền, trong khi giảm tín hiệu có thể sử dụng.  

**HƯỚNG DẪN SỬ DỤNG**  

Sử dụng trên nền tảng Google Colab  

**1. Tạo một file Google Colab trong folder Colab Notebooks**

**2. Đầu tiên cài các thư viện cần thiết cho bài toán  **
```
!pip install opencv-python-headless matplotlib
!pip install flask
```
**3. Thay đổi đường dẫn đến thư mục bạn mong muố**n
```
cd /content/drive/MyDrive/Colab Notebooks/bao_cao
```
**4. Sử dụng đoạn mã sau để tạo 1 URL proxy cho cổng 5000 để chạy một ứng dụng web trên Google Colab**
```
from google.colab.output import eval_js
print(eval_js("google.colab.kernel.proxyPort(5000)"))
```
**5. Nội dung code python**
```
from flask import Flask, render_template, request
import cv2
import numpy as np
import base64

app = Flask(__name__)

@app.route('/', methods=['GET', 'POST'])
def upload_file():
    if request.method == 'POST':
        file = request.files['file']
        img = cv2.imdecode(np.frombuffer(file.read(), np.uint8), cv2.IMREAD_UNCHANGED)
        if len(img.shape) == 3:
            img = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
        equ = cv2.equalizeHist(img)
        _, buffer = cv2.imencode('.jpg', equ)
        img_str = base64.b64encode(buffer).decode()
        return render_template('index.html', img_data=img_str)
    return render_template('index.html')

if __name__ == '__main__':
    app.run()
```
**6. Tạo 1 folder Templates trong Drive chứa đoạn code Python trên.  **

**7. Tạo 1 file Html để hiển thị ảnh**

**8. Nội dung code Html**
```
<!doctype html>
<html>
<head>
    <title>Histogram Equalization</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f0f0f0;
        }
        .container {
            width: 80%;
            margin: auto;
            padding: 20px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            background-color: #fff;
            border-radius: 8px;
        }
        h1, h2 {
            color: #333;
        }
        form {
            display: flex;
            flex-direction: column;
            gap: 10px;
        }
        input[type="file"] {
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 4px;
        }
        input[type="submit"], button {
            background-color: #4CAF50;
            color: white;
            padding: 10px 20px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            transition: background-color 0.3s ease;
        }
        input[type="submit"]:hover, button:hover {
            background-color: #45a049;
        }
        #image-container img {
            max-width: 100%;
            height: auto;
        }
        #status {
            background-color: #ddd;
            padding: 10px;
            border-radius: 4px;
            margin-top: 20px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Histogram Equalization</h1>
        <form action="/" method="post" enctype="multipart/form-data">
            <input type="file" name="file" onchange="loadImage(event)">
            <input type="submit" value="Upload">
        </form>
        <button onclick="compareImages()">So sánh hình ảnh</button>
        <h2>Current MRI Image:</h2>
        <div id="image-container"></div>
        <h2>Status:</h2>
        <div id="status"></div>
        {% if img_data %}
        <h2>Result:</h2>
        <img src="data:image/jpeg;base64,{{ img_data }}" id="result-image">
        {% endif %}
    </div>
    <script>
        var currentImage = null;

        function loadImage(event) {
            var image = document.createElement('img');
            image.src = URL.createObjectURL(event.target.files[0]);
            document.getElementById('image-container').innerHTML = '';
            document.getElementById('image-container').appendChild(image);
            currentImage = event.target.files[0];
            updateStatus();
        }

        function updateStatus() {
            if (currentImage) {
                var status = 'Size: ' + currentImage.size + ' bytes<br>';
                status += 'Type: ' + currentImage.type;
                document.getElementById('status').innerHTML = status;
            }
        }

        function compareImages() {
            var resultImage = document.getElementById('result-image');
            if (currentImage && resultImage) {
                var compareContainer = document.createElement('div');
                var originalImage = document.createElement('img');
                originalImage.src = URL.createObjectURL(currentImage);
                compareContainer.appendChild(originalImage);
                compareContainer.appendChild(resultImage.cloneNode(true));
                document.getElementById('image-container').innerHTML = '';
                document.getElementById('image-container').appendChild(compareContainer);
            }
        }
    </script>
</body>
</html>
```
**9. Quay lại file python và chạy toàn bộ theo thứ tự**

**10. Click vào dòng link được hiển thị trong mã**
```
from google.colab.output import eval_js
print(eval_js("google.colab.kernel.proxyPort(5000)"))
```
**11. Trang web giao diện người dùng được hiển thị ra**
![image](https://github.com/TranMinhQuan01/Bao_Cao/assets/97246847/c895eb43-b0fb-4b13-aba7-9c480ec4d375)

**12. Click vào "Chọn tệp" để thêm hình ảnh muốn sửa đổi**
![image](https://github.com/TranMinhQuan01/Bao_Cao/assets/97246847/8556a671-5238-4bbb-9aac-f37e69b8d1b0)

**13. Click vào "Upload" để chuyển đổi hình ảnh**
![image](https://github.com/TranMinhQuan01/Bao_Cao/assets/97246847/b8de62b9-a120-4e0f-94d9-a60b2eb9d6f9)
