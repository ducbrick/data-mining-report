# Introduction

## General info

Nhiều ứng dụng trong khai phá dữ liệu đòi hỏi phải xác định các đối tượng, mẫu, thuộc tính và sự kiện tương tự hoặc khác biệt trong tập dữ liệu. Nói cách khác, cần có một cách tiếp cận để định lượng mức độ tương đồng giữa các đối tượng dữ liệu. Hầu như mọi bài toán trong khai phá dữ liệu, chẳng hạn như phân cụm, phát hiện ngoại lai hay phân loại, đều yêu cầu phải tính toán độ tương đồng giữa các đối tượng. (Book2 pg89)

Chương này sẽ giới thiệu về cách biểu diễn các đối tượng dưới dạng các tập hợp, cũng như các cách nén các tập hợp lớn sao cho ta vẫn có thể suy luận được độ tương đồng giữa các tập đã nén. Tuy nhiên, một số khái niệm về "độ tương đồng" không cho phép ta biểu diễn dưới dạng giao của các tập hợp. Do đó, ta cần phải tìm hiểu về các thước đo khoảng cách trong những không gian tổng quát hơn, để phù hợp với nhiều định nghĩa khác nhau của khái niệm “tương đồng.”

Một vấn đề quan trọng khác sẽ được đề cập trong chương này là khi làm việc với dữ liệu lớn (bigdata), ta có thể có quá nhiều đối tượng để kiểm tra độ tương đồng giữa từng cặp phần tử, kể cả khi việc tính độ tương đồng của chúng rất dễ. Cuối cùng, ta đi đến các ứng dụng của việc tìm độ tương đồng trong thực tế. (Book4 pg91)