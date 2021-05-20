# Lane_line_detection
#Cài đặt 
* Python 3.5
* Numpy
* OpenCV-Python
* Matplotlib
* Pickle
# Hướng giải quyết bài toán
1. Hiệu chỉnh camera
  1.1 Chuyển qua ảnh xám
  1.2 Tìm các góc trên bàn cờ vua bằng hàm: findChessboardCorners() với kích thước 9x6
  1.3 Hiệu chuẩn hình ảnh: tính toán ma trận biến dạng với calibrateCamera() của openCV
  1.4 Sử dụng ma trận biến dạng để đưa hình ảnh về không biến dạng với hàm undistort() của openCV
2. Đưa ảnh về dạng không biến dạng
  2.1 Sử dụng ma trận biến dạng ở trên để đưa hình ảnh về không biến dạng với hàm undistort() của openCV 
3. Phân ngưỡng hình ảnh
  3.1 Phân ngưỡng hình ảnh với ảnh không biến dạng ở trên 
    * áp dụng Absolute horizontal Sobel cho ảnh 
    * toán tử Sobel cho ngang dọc và tính toán độ lớn
    * toán tử Sobel tính hướng gradient 
    * chuyển ảnh RGB sang HLS với ngưỡi S
    => kết hợp tất cả ở trên để tạo ra ảnh nhị phân cuối cùng(  combined_thresh.py.py với hàm combined_thresh() )
4. Chuyển đổi phối cảnh  ( mục đích để có được tầm nhìn toàn cảnh của làn đường từ đó điều chỉnh đường cong với các làn đường) 
  4.1 Biến đổi phối cảnh với ảnh nhị phân ở trên 
  4.2 sử dụng hàm getPerspectiveTransform() và warpPerspective() của OpenCV 
  4.3 Xác định các điẻm đầu và điểm đến bằng cách thủ công  ( có thể sử dụng thuật tóan) 
  4.4 code trong file perspective_transform.py' với hàm perspective_transform()
5. Sử dụng đa thức phù hợp cho làn bên trái và làn bên phải ( điều chỉnh đường cong theo làn đương) 
  5.1 với ảnh nhị phân bị cong vệnh phù hợp đa thức bậc 2 cho làn trái và làn phải ( hàm line_fit() của line_fit.py)
     * Tính toán biểu đồ của nửa dưới của hình ảnh
     * Phân vùng hình ảnh thành 9 lát ngang
     * Bắt đầu từ lát dưới cùng, đặt một cửa sổ rộng 200 pixel xung quanh đỉnh bên trái và đỉnh bên phải của biểu đồ (chia biểu đồ làm đôi theo chiều dọc)
     * Đi lên các lát cửa sổ ngang để tìm các pixel có khả năng là một phần của làn đường bên trái và bên phải, gần các cửa sổ trượt một cách cơ hội
     * Đưa ra 2 nhóm pixel (pixel ứng viên của đường làn bên trái và bên phải), điều chỉnh một đa thức bậc 2 cho mỗi nhóm, 
       đại diện cho các đường làn đường bên trái và bên phải ước tính
  5.2 
6. Tính bán kính cong 
  6.1 Tính bán kính cong cho mỗi đường và chuyển khoảng cách từ pixel sang mét 
  6.2 Tính bán kính cong trung bình cho làn bên trái và làn bên phải (hàm calc_curve() trong fiel line_fit.py)
7. Độ lệch của xe so với tâm làn đường
  7.1 thông qua đa thức phù hợp xác định được độ lệch của xe so với tâm làn đường
  7.2 Giả sử tâm của xe là tâm của hình ảnh => tâm của làn đường là giá trị trung bình của giá trị x dưới cùng của làn bên trái và bên phải 
      Độ lệch là giá trị tâm của phương tiện trừ đi giá trị tâm của làn đường ( calc_vehicle_offset() trong line_fit.py )
8. Vẽ khu vực của làn đường, thông tin độ cong của làn và độ lệch phương tiện
  * Tạo một hình ảnh trống và vẽ các đường polyfit của chúng tôi (ước tính các đường làn bên trái và bên phải)
  * Tô khu vực giữa các dòng (với màu xanh lá cây)
  * Sử dụng ma trận sợi dọc nghịch đảo được tính toán từ phép biến đổi phối cảnh, để "làm chậm" ở trên sao cho nó được căn chỉnh với phối cảnh của hình ảnh ban  đầu
  * Phủ chú thích ở trên lên hình ảnh gốc
  * Thêm văn bản vào hình ảnh gốc để hiển thị độ cong của làn đường và độ lệch của phương tiện
