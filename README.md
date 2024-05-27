# Simulator measures the influence of people on AGVs using Social Force Model

## Social Force Model
The Social Force Model (SFM) is a mathematical model used to describe the movement of individuals in crowded spaces. It was first proposed by Dirk Helbing and Péter Molnár in 1995. 

The model assumes that individuals move based on two types of forces: physical forces and social forces. Physical forces are those that are governed by laws of physics, such as inertia, friction, and gravity. Social forces, on the other hand, are those that are influenced by the behavior of other individuals in the crowd

The SFM model also takes into account the personal preferences of individuals, such as their desired walking speed and preferred direction of movement. These preferences are included in the model as parameters that can be adjusted to simulate different scenarios.

Throughout the years, many improvements were made to the original model. One of the latest improvement was made
by [Moussaïd *et al.* (2009)](https://doi.org/10.1098/rspb.2009.0405). In their research, the model parameters were
calibrated to match the results of the experiment they have conducted on the real-world crowd. This simulator is created based on this research.

## Getting Started

### Prerequisites

This project requires the following library.
- [Open Graphics Library (OpenGL)](https://www.opengl.org/)

This project also requires users to use compilers that support C++ 11.

### Run simulation

**The emulator is run with the following input:**
- input.json: The configuration of the emulator scenarios
- map.txt: Map data (extracted from the OMNeT++ simulator map)

<!-- **Create a Pointer to the <code>SocialForce</code> Object** -->
**Main commands** <br/>
To compile program
```cpp
make
```
To run simulator

```cpp
./app
```



#include <iostream>
#include <vector>
#include <random>
#include <cmath>

// Hàm sinh số thực ngẫu nhiên trong khoảng [min, max]
double generateRandomDouble(double min, double max) {
    std::random_device rd;
    std::mt19937 gen(rd());
    std::uniform_real_distribution<double> dis(min, max);
    return dis(gen);
}

// Hàm tạo mẫu dữ liệu tuân theo phân phối chuẩn với các giá trị khác nhau
std::vector<double> generateSample(int n, double min_value, double max_value) {
    // Tính toán trung bình và độ lệch chuẩn
    double mean = (min_value + max_value) / 2;
    double std_dev = (max_value - min_value) / 6;

    // Tạo mẫu dữ liệu với các giá trị tuân theo phân phối chuẩn
    std::vector<double> sample(n);
    for (int i = 0; i < n; ++i) {
        // Tạo các giá trị tuân theo phân phối chuẩn với PDF giảm dần
        double x = generateRandomDouble(0.0, 1.0);
        double y = -std_dev * log(x);
        if (i % 2 == 0) {
            sample[i] = mean + y;
        } else {
            sample[i] = mean - y;
        }
        // Giới hạn giá trị trong khoảng [min_value, max_value]
        sample[i] = std::clamp(sample[i], min_value, max_value);
    }
    return sample;
}

// Hàm lấy giá trị nhỏ nhất trong mẫu dữ liệu
double getMin(const std::vector<double>& sample) {
    return *std::min_element(sample.begin(), sample.end());
}

// Hàm lấy giá trị lớn nhất trong mẫu dữ liệu
double getMax(const std::vector<double>& sample) {
    return *std::max_element(sample.begin(), sample.end());
}

int main() {
    int n = 30000;
    double min_value = 5.0;
    double max_value = 104.0;

    std::vector<double> sample = generateSample(n, min_value, max_value);

    // In ra mẫu dữ liệu
    std::cout << "Mẫu dữ liệu là: \n";
    for (double val : sample) {
        std::cout << val << " ";
    }
    std::cout << std::endl;

    std::cout << "Giá trị nhỏ nhất là: " << getMin(sample) << std::endl;
    std::cout << "Giá trị lớn nhất là: " << getMax(sample) << std::endl;

    return 0;
}
