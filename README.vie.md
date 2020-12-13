# Tìm tập tin trùng lắp (Duplicate Files Finder)

Chúng ta thường tải xuống hoặc sao chép rất nhiều tập tin từ các nguồn khác nhau và đôi khi vô tình lưu trữ cùng một tệp nhiều lần trong các thư mục khác nhau của máy tính. Đây là nơi sự lộn xộn bắt đầu.

```text
> find . -name "*.jpg" -exec stat -f '%z %N' $PWD/{} \;
3083 /home/botnet/downloads/heobs/archive.csv
309753 /home/botnet/downloads/heobs/GL0625.jpg
483520 /home/botnet/downloads/heobs/GL0701.jpg
309753 /home/botnet/downloads/heobs/GL1240.jpg
309753 /home/botnet/downloads/heritagego/GL0625.jpg
483520 /home/botnet/downloads/heritagego/GL0701.jpg
627451 /home/botnet/downloads/heritagego/GL0803.jpg
309753 /home/botnet/downloads/heritagego/GL1240.jpg
```

Một số tập tin có thể đã được sao chép nhiều lần ở các vị trí khác nhau với các tên có thể khác nhau, ví dụ:

```text
309753 /home/botnet/downloads/heobs/GL0625.jpg
309753 /home/botnet/downloads/heobs/GL1240.jpg
309753 /home/botnet/downloads/heritagego/GL0625.jpg
309753 /home/botnet/downloads/heritagego/GL1240.jpg
```

Chúng ta cần tìm các tập tin trùng lặp có cùng nội dung nhưng không nhất thiết phải cùng tên. Nhiệm vụ của bạn là viết [Command-Line Interface (CLI)](https://en.wikipedia.org/wiki/Command-line_interface) Tập lệnh Python sẽ trả về danh sách các tệp trùng lặp được xác định bởi đường dẫn và tên tuyệt đối của chúng.

## Nhiệm vụ 1: Viết đoạn mã khung xương bằng ngôn ngữ Python (Python Script Skeleton)

Soạn một tập tin có tên `find_duplicate_files.py` chứa mã Python có thể thực thi được, tập tin này chấp nhận các tham số `-p` hoặc `--path` để có thể nhận vào thông tin đường dẫn thư mục gốc và bắt đầu tìm kiếm những tập tin trùng lắp bên trong. Ví dụ:

```shell
$ ./find_duplicate_files.py --path ~/whatever-directory
```

Bạn BẮT BUỘC phải sử dụng mô-đun chuẩn của Python [argparse](https://docs.python.org/3/library/argparse.html) để có thể trích xuất các lựa chọn, tham số hoặc các lệnh phụ từ dòng lệnh thực thi

## Nhiệm vụ 2: Tìm kiếm tất cả các tệp tin

Viết hàm `scan_files` với đối số đầu vào có tên `path` tương ứng với thông tin đường dẫn thư mục gốc, hàm này sẽ trả về một danh sách các tập tin được tìm thấy bao gồm luôn đường dẫn đến tập tin đó. Ví dụ:

```python
>>> scan_files('~/downloads')
['/home/botnet/downloads/heobs/archive.csv',
'/home/botnet/downloads/heobs/GL0625.jpg',
'/home/botnet/downloads/heobs/GL0701.jpg',
'/home/botnet/downloads/heobs/GL1240.jpg',
'/home/botnet/downloads/heritagego/GL0625.jpg',
'/home/botnet/downloads/heritagego/GL0701.jpg',
'/home/botnet/downloads/heritagego/GL0803.jpg',
'/home/botnet/downloads/heritagego/GL1240.jpg]
```

Bạn BẮT BUỘC phải sử dụng hàm [os.walk](https://docs.python.org/3/library/os.html#os.walk) của Python để trích xuất danh sách các tập tin trong từng thư mục bắt đầu từ thư mục gốc `path`.

_Lưu ý: hàm `scan_files` BẮT BUỘC phải bỏ qua các thư mục chỉ là liên kết tượng trưng ([symbolic links](https://en.wikipedia.org/wiki/Symbolic_link)) bên trong thư mục gốc._

## Nhiệm vụ 3: Nhóm tất cả các tập tin theo kích thước

Các tập tin trùng lặp có cùng kích thước.

Viết một hàm tên `group_files_by_size` nhận đối số (argument) bằng tham số (parameter) `file_path_names`, tương ứng với danh sách đường dẫn tuyệt đối của tập tin, hàm này sẽ trả về danh sách các nhóm trong đó có một nhóm chứa hai tập tin có cùng kích thước.

Bạn BẮT BUỘC phải bỏ qua các tập tin có kích thước rỗng (`0` bytes). Ví dụ:

```python
>>> file_path_names = [
'/home/botnet/downloads/heobs/archive.csv',
'/home/botnet/downloads/heobs/GL0625.jpg',
'/home/botnet/downloads/heobs/GL0701.jpg',
'/home/botnet/downloads/heobs/GL1240.jpg',
'/home/botnet/downloads/heritagego/GL0625.jpg',
'/home/botnet/downloads/heritagego/GL0701.jpg',
'/home/botnet/downloads/heritagego/GL0803.jpg',
'/home/botnet/downloads/heritagego/GL1240.jpg']
>>> group_files_by_size(file_path_names)
[['/home/botnet/downloads/heobs/GL0701.jpg',
'/home/botnet/downloads/heritagego/GL0701.jpg'],
['/home/botnet/downloads/heobs/GL0625.jpg',
'/home/botnet/downloads/heritagego/GL0625.jpg',
'/home/botnet/downloads/heobs/GL1240.jpg',
'/home/botnet/downloads/heritagego/GL1240.jpg']]
```

## Nhiệm vụ 4: Tạo giá trị Hash (Hash Value) cho tập tin

Nội dung tập tin có thể được chuyển/băm thành [checksum](https://en.wikipedia.org/wiki/Checksum) (hash), cũng được biết là [message digest](https://www.techopedia.com/definition/4024/message-digest)

Quá trình chuyển/băm nội dung của một tập tin thành checksum được gọi là hàm checksum hoặc [checksum algorithm](https://en.wikipedia.org/wiki/Cryptographic_hash_function).

Có nhiều thuật toán chuyển/băm (cryptographic hash algorithms). [MD5 message-digest algorithm](https://en.wikipedia.org/wiki/MD5) là một hàm chuyển/băm được sử dụng rộng rãi tạo ra giá trị chuyển/băm 128 bit. Thuật toán MD5 có thể được sử dụng để tạo fingerprint thu nhỏ của một tập tin.

Các tập tin có cùng nội dung được xác định có cùng giá trị chuyển/băm.

_Lưu ý: khó có khả năng hai tập tin có nội dung khác nhau mà có cùng giá trị chuyển/băm MD5._

Viết hàm `get_file_checksum` nhận đối số bằng tham số `file_path_name`, tương ứng với đường dẫn tuyệt đối và tên của tập tin, hàm này sẽ trả về giá trị chuyển/băm MD5 của nội dung của các tập tin này. Ví dụ:

```python
>>> get_file_checksum('/home/botnet/downloads/heobs/GL0625.jpg')
'dd23819ce306f0f1476522c9ce3e0a07'
```
Bạn BẮT BUỘC phải sử dụng mô-đun Python [hashlib](https://docs.python.org/3/library/hashlib.html) để tạo giá trị chuyển/băm của nội dung tập tin.

## Nhiệm vụ 5: Nhóm các tệp tin dựa trên Checksum 

Viết hàm `group_files_by_checksum` nhận đối số bằng tham số `file_path_names`, tương ứng với danh sách đường dẫn tuyệt đối và tên của các tập tin, hàm này sẽ trả về danh sách các nhóm tập tin trùng lắp. Ví dụ:

```python
>>> file_path_names = [
'/home/botnet/downloads/heobs/GL0625.jpg',
'/home/botnet/downloads/heritagego/GL0625.jpg',
'/home/botnet/downloads/heobs/GL1240.jpg',
'/home/botnet/downloads/heritagego/GL1240.jpg']
>>> group_files_by_checksum(file_path_names)
[['/home/botnet/downloads/heobs/GL0625.jpg',
'/home/botnet/downloads/heritagego/GL0625.jpg'],
['/home/botnet/downloads/heobs/GL1240.jpg',
'/home/botnet/downloads/heritagego/GL1240.jpg']]
```

Hàm `group_duplicate_files` BẮT BUỘC phải sử dụng hàm `get_file_checksum` để kiểm tra và phát hiện tập tin trùng lắp.

_Lưu ý: trong thực tế, đối số của hàm này nên nhận danh sách các tập tin có cùng kích thước, đại loại như khả năng trùng lắp của các tập tin có cùng kích thước là cao nhất. Sẽ không tối ưu nếu truyền vào danh sách các tập tin có kích khác nhau._

## Nhiệm vụ 6: Tìm kiếm tất cả các tập tin trùng lắp

Viết hàm `find_duplicate_files` nhận đối số bằng tham số `file_path_names`, tương ứng với danh sách đường dẫn tuyệt đối và tên của các tập tin, hàm này sẽ trả về danh sách các nhóm tập tin trùng lắp. Ví dụ:

```python
>>> file_path_names = ['/home/botnet/downloads/heobs/GL0701.jpg',
'/home/botnet/downloads/heobs/GL0625.jpg',
'/home/botnet/downloads/heobs/GL1240.jpg',
'/home/botnet/downloads/heobs/archive.csv',
'/home/botnet/downloads/heritagego/GL0701.jpg',
'/home/botnet/downloads/heritagego/GL0625.jpg',
'/home/botnet/downloads/heritagego/GL1240.jpg',
'/home/botnet/downloads/heritagego/GL0803.jpg']
>>> find_duplicate_files(file_path_names)
[['/home/botnet/downloads/heobs/GL0701.jpg',
'/home/botnet/downloads/heritagego/GL0701.jpg'],
['/home/botnet/downloads/heobs/GL0625.jpg',
'/home/botnet/downloads/heritagego/GL0625.jpg'],
['/home/botnet/downloads/heobs/GL1240.jpg',
'/home/botnet/downloads/heritagego/GL1240.jpg']]
```

_Lưu ý: hàm `find_duplicate_files` BẮT BUỘC phải sử dụng hai hàm `group_files_by_size` và `group_files_by_checksum` trong các nhiệm vụ trên_

## Nhiệm vụ 7: Xuất kết quả sang dạng JSON

Hoàn thành đoạn mã Python bằng cách xuất kết quả sang dạng JSON tương ứng với danh sách các tập tin trùng lắp. Ví dụ:

```shell
$ ./find_duplicate_files.py --path ~/downloads
[["/home/botnet/downloads/heobs/GL0701.jpg",
"/home/botnet/downloads/heritagego/GL0701.jpg"],
["/home/botnet/downloads/heobs/GL0625.jpg",
"/home/botnet/downloads/heritagego/GL0625.jpg"],
["/home/botnet/downloads/heobs/GL1240.jpg",
"/home/botnet/downloads/heritagego/GL1240.jpg"]]
```

Bạn BẮT BUỘC phải sử dụng mô-đun [`json`](https://docs.python.org/3/library/json.html) để chuyển hóa danh sách các tập tin trùng lắp sang chuỗi định dạng JSON.

## Nhiệm vụ 8: Tối ưu hóa hiệu suất (Điểm thưởng)

Tìm một phương pháp khác để tìm các tập tin trùng lặp nhanh hơn nhiều so với sử dụng thuật toán băm.

_Lưu ý: bạn KHÔNG THỂ trực tiếp sử dụng mô-đun [filecmp](https://docs.python.org/3/library/filecmp.html), việc này quá dễ dàng! - nhưng bạn chắc chắn được phép lấy cảm hứng từ mô-đun này. ;)_

## Công cụ: Duplicate Files Generator

Chúng tôi cung cấp cho bạn Python CLI được sử dụng để tạo các tập tin trùng lặp có kích thước ngẫu nhiên để giúp bạn kiểm tra các đoạn mã của chính bạn.

Công cụ này hỗ trợ một số tùy chọn. Nhập `-h` hoặc` --help` để có danh sách các tùy chọn.

Ví dụ: giả sử bạn muốn tạo 10.000 tập tin trong thư mục gốc `test`, với 30% các tệp này được sao chép, bạn chỉ cần nhập lệnh sau: 

```shell
$ ./generate_duplicate_files.py --file-count 10000 --duplicate-file-ratio 0.3 --path test
```
