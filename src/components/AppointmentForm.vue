<template>
  <div class="appointment-form-modal" v-if="show">
    <div class="form-container" v-if="showForm">
      <div class="form-header">
        <h3>预约挂号</h3>
        <button @click="closeForm">×</button>
      </div>

      <form @submit.prevent="submitForm" class="two-column-form">
        <div class="form-column">
          <div class="form-group">
            <label>患者姓名</label>
            <input v-model="formData.name" required />
          </div>

          <div class="form-group">
            <label>患者年龄</label>
            <input type="number" v-model="formData.age" required />
          </div>

          <div class="form-group">
            <label>患者性别</label>
            <select v-model="formData.gender" required>
              <option value="男">男</option>
              <option value="女">女</option>
            </select>
          </div>

          <div class="form-group">
            <label>预约科室</label>
            <input v-model="formData.department" readonly />
          </div>
        </div>

        <div class="form-column">
          <div class="form-group">
            <label>预约医生</label>
            <div v-if="loading" class="loading-text">正在加载医生列表...</div>
            <div v-else-if="error" class="error-text">{{ error }}</div>
            <template v-else>
              <div class="debug-info" style="display: none">
                v-model="formData.doctor"
              </div>
              <select
                v-model="formData.doctor"
                class="form-group select"
                required
                :disabled="loading || filteredDoctors.length === 0"
              >
                <option value="" disabled>请选择医生</option>
                <option
                  v-for="doctor in filteredDoctors"
                  :key="doctor.id"
                  :value="doctor"
                >
                  {{ doctor.name }}
                </option>
              </select>
              <div v-if="filteredDoctors.length === 0" class="empty-text">
                当前科室暂无可用医生，请稍后再试
              </div>
            </template>

            <div
              v-if="formData.doctor && typeof formData.doctor === 'object'"
              class="doctor-details"
            >
              <h4>预约详情</h4>
              <div class="doctor-info">
                <img
                  v-if="formData.doctor.photo"
                  :src="formData.doctor.photo"
                  alt="医生照片"
                  class="doctor-photo"
                />
                <div class="doctor-text">
                  <p><strong>医生姓名:</strong> {{ formData.doctor.name }}</p>
                  <p>
                    <strong>医生性别:</strong>
                    {{ formData.doctor.gender || "未知" }}
                  </p>
                  <p>
                    <strong>医生年龄:</strong>
                    {{ formData.doctor.age || "未知" }}
                  </p>
                  <p>
                    <strong>医生职称:</strong>
                    {{ formData.doctor.title || "医生" }}
                  </p>
                  <p v-if="formData.doctor.dfloor">
                    <strong>科室楼层:</strong> {{ formData.doctor.dfloor }}
                  </p>
                  <p v-if="formData.doctor.specialty">
                    <strong>专长:</strong> {{ formData.doctor.specialty }}
                  </p>
                  <p v-if="formData.doctor.description">
                    <strong>简介:</strong> {{ formData.doctor.description }}
                  </p>
                </div>
              </div>
            </div>
          </div>

          <div class="form-group">
            <label>预约时间</label>
            <input type="datetime-local" v-model="formData.time" required />
          </div>
        </div>
        <div class="form-actions">
          <button type="submit">提交预约</button>
          <button type="button" @click="closeForm">取消</button>
        </div>
      </form>
    </div>
    
    <div v-if="showSuccess" class="appointment-success">
      <div class="success-image">
        <img src="@/assets/images/success-icon.svg" alt="预约成功" />
      </div>
      <h3>预约成功</h3>
      <div class="success-content">
        <p>{{ appointmentResult.name }} {{ appointmentResult.gender === '男' ? '先生' : '女士' }}</p>
        <div class="appointment-details">
          <p><strong>医生:</strong> {{ appointmentResult.doctorName }}</p>
          <p><strong>科室:</strong> {{ appointmentResult.department }}</p>
          <p><strong>楼层:</strong> {{ appointmentResult.floor }}楼</p>
          <p><strong>时间:</strong> {{ appointmentResult.time }}</p>
        </div>
        <div class="countdown">
          窗口将在 {{ countdown }} 秒后自动关闭
        </div>
      </div>
      <button class="close-button" @click="closeForm">关闭</button>
    </div>
  </div>
</template>

<script>
import axios from "axios";

// 创建配置好的axios实例
// 配置API客户端
const apiClient = axios.create({
  baseURL: "/api",
  headers: {
    "Content-Type": "application/json",
    "Accept": "application/json"
  }
});

// 请求拦截器 - 记录完整的请求信息
apiClient.interceptors.request.use(config => {
  console.groupCollapsed(`请求: ${config.method.toUpperCase()} ${config.url}`);
  console.log("请求配置:", config);
  console.log("请求数据:", config.data);
  console.log("请求头:", config.headers);
  console.groupEnd();
  
  // 确保数据被正确序列化
  if (config.data && typeof config.data === 'object') {
    config.data = JSON.stringify(config.data);
  }
  return config;
}, error => {
  console.error("请求拦截器错误:", error);
  return Promise.reject(error);
});

// 响应拦截器 - 记录完整的响应信息
apiClient.interceptors.response.use(response => {
  console.groupCollapsed(`响应: ${response.config.method.toUpperCase()} ${response.config.url}`);
  console.log("响应状态:", response.status);
  console.log("响应数据:", response.data);
  console.log("响应头:", response.headers);
  console.groupEnd();
  return response;
}, error => {
  if (error.response) {
    console.groupCollapsed(`错误响应: ${error.config.method.toUpperCase()} ${error.config.url}`);
    console.log("错误状态:", error.response.status);
    console.log("错误数据:", error.response.data);
    console.log("错误头:", error.response.headers);
    console.groupEnd();
  } else {
    console.error("请求错误:", error.message);
  }
  return Promise.reject(error);
});

export default {
  name: "AppointmentForm",
  props: {
    show: Boolean,
    department: String,
    departmentId: {
      type: Number,
      required: true,
    },
  },
  data() {
    return {
      formData: {
        name: "",
        age: "",
        gender: "",
        department: this.department,
        doctor: "",
        time: "",
      },
      allDoctors: [],
      loading: false,
      error: null,
      submitting: false,
      showForm: true,
      showSuccess: false,
      appointmentResult: {
        name: "",
        gender: "",
        doctorName: "",
        department: "",
        floor: "",
        time: ""
      },
      countdown: 5
    };
  },
  watch: {
    departmentId: {
      immediate: true, // 立即执行
      async handler(newId) {
        if (!newId) return;

        this.loading = true;
        this.error = null;
        try {
          const apiUrl = `/api/doctor/department/${newId}`;
          console.log("1. 请求API:", apiUrl);

          const response = await apiClient.get(apiUrl.replace('/api', ''));
          console.log("2. 完整API响应:", response);
          console.log("3. 响应状态码:", response.status);
          console.log("4. 响应数据:", response.data);

          // 深度检查响应数据结构
          if (!response.data) {
            console.error("5. 错误: 响应数据为空");
            throw new Error("API返回空数据");
          }

          // 处理不同格式的响应数据
          let doctors = [];
          if (Array.isArray(response.data)) {
            console.log("6. 响应数据是数组格式");
            doctors = response.data;
          } else if (response.data.doctors) {
            console.log("6. 响应数据包含doctors字段");
            doctors = response.data.doctors;
          } else if (response.data.data) {
            console.log("6. 响应数据包含data字段");
            doctors = response.data.data.doctors || response.data.data;
          } else {
            console.error("7. 错误: 无法识别的响应格式", response.data);
            throw new Error("无法识别的API响应格式");
          }

          console.log("8. 处理后医生数据:", doctors);
          // 转换API返回的医生数据格式
          this.allDoctors = Array.isArray(doctors)
            ? doctors.map((doctor) => ({
                id: doctor.id,
                name: doctor.docName,
                gender: doctor.docGender,
                title: doctor.docClass,
                age: doctor.docAge,
                department: doctor.department,
                dfloor: doctor.dfloor,
                specialty: doctor.specialty,
                description: doctor.description,
                photo: doctor.photo,
                // 保留原始数据用于调试
                _raw: doctor,
              }))
            : [];

          console.log("转换后的医生数据:", this.allDoctors);

          // 设置默认选中的医生（第一个）
          if (this.allDoctors.length > 0) {
            this.formData.doctor = this.allDoctors[0];
          }
        } catch (error) {
          console.error("9. 获取医生列表失败:", error);
          if (error.response) {
            console.error("10. 错误响应:", {
              status: error.response.status,
              data: error.response.data,
              headers: error.response.headers,
            });
            this.error = `请求失败: ${error.response.status} - ${
              error.response.data.message || "未知错误"
            }`;
          } else if (error.request) {
            console.error("11. 无响应:", error.request);
            this.error = "服务器无响应，请检查网络连接";
          } else {
            console.error("12. 请求配置错误:", error.message);
            this.error = `请求配置错误: ${error.message}`;
          }

          this.allDoctors = [];
        } finally {
          this.loading = false;
          console.log("13. 最终状态:", {
            loading: this.loading,
            error: this.error,
            doctorsCount: this.allDoctors.length,
          });
        }
      },
    },
    department(newVal) {
      this.formData.department = newVal;
    },
  },
  computed: {
    filteredDoctors() {
      return Array.isArray(this.allDoctors) ? this.allDoctors : [];
    },
  },
  methods: {
    closeForm() {
      this.$emit("close");
    },
    async submitForm() {
      // 前端验证必填字段
      if (!this.formData.name || !this.formData.age || !this.formData.gender || 
          !this.formData.doctor || !this.formData.time) {
        alert("请填写所有必填字段");
        return;
      }

      this.submitting = true;
      this.showSuccess = false;

      try {
        // 准备提交数据 - 添加字段验证和格式处理
        const requestData = {
          pName: this.formData.name.trim(),
          pAge: parseInt(this.formData.age),
          pGender: this.formData.gender,
          dName: this.formData.department,
          docName: this.formData.doctor.name,
          docClass: this.formData.doctor.title || "医生",
          dFloor: this.formData.doctor.dfloor || "1",
          pDatetime: new Date(this.formData.time).toISOString(), // 确保日期格式
          pStatus: "待就诊"
        };

        // 验证字段映射
        const requiredFields = [
          'pName', 'pAge', 'pGender', 'dName', 
          'docName', 'docClass', 'dFloor', 'pDatetime'
        ];
        
        const missingFields = requiredFields.filter(field => !requestData[field]);
        if (missingFields.length > 0) {
          throw new Error(`缺少必要字段: ${missingFields.join(', ')}`);
        }

        console.group("提交数据验证");
        console.log("字段映射:", {
          '姓名': 'pName',
          '年龄': 'pAge',
          '性别': 'pGender',
          '科室': 'dName',
          '医生': 'docName',
          '职称': 'docClass',
          '楼层': 'dFloor',
          '时间': 'pDatetime',
          '状态': 'pStatus'
        });
        console.log("实际提交数据:", requestData);
        console.groupEnd();

        // 使用配置好的apiClient发送请求
        console.group("准备提交预约数据");
        console.log("原始数据:", requestData);
        console.log("数据类型:", typeof requestData);
        
        // 打印完整的请求数据
        console.group("预约请求数据");
        console.log("请求URL:", "/api/order/add");
        console.log("请求方法:", "POST");
        console.log("请求头:", {
          "Content-Type": "application/json",
          "Accept": "application/json"
        });
        console.log("请求体:", JSON.stringify(requestData, null, 2));
        console.groupEnd();

        try {
          // 发送请求
          const response = await apiClient.post("/order/add", requestData);
          
          // 打印完整响应
          console.group("预约响应数据");
          console.log("响应状态:", response.status);
          console.log("响应头:", response.headers);
          console.log("响应数据:", response.data);
          console.groupEnd();

          // 处理响应
          if (response.data && response.data.success) {
            // 设置预约结果数据
            this.appointmentResult = {
              name: this.formData.name,
              gender: this.formData.gender,
              doctorName: this.formData.doctor.name,
              department: this.formData.department,
              floor: this.formData.doctor.dfloor || "1",
              time: new Date(this.formData.time).toLocaleString()
            };
            
            // 显示成功信息并开始倒计时
            this.showSuccess = true;
            this.submitting = false;
            this.countdown = 5;
            
            const timer = setInterval(() => {
              this.countdown--;
              if (this.countdown <= 0) {
                clearInterval(timer);
                this.closeForm();
              }
            }, 1000);
            
            return response;
          } else {
            const errorMsg = response.data?.message || "预约失败，未知原因";
            this.submitting = false;
            this.$message.error(errorMsg);
            throw new Error(errorMsg);
          }
        } catch (error) {
          console.error("API请求失败:", error);
          let errorMsg = "预约提交失败，请稍后再试";
          
          if (error.message.includes("缺少必要字段")) {
            errorMsg = error.message;
          } else if (error.response) {
            console.error("响应数据:", error.response.data);
            console.error("响应状态:", error.response.status);
            
            // 根据HTTP状态码提供更具体的错误信息
            switch(error.response.status) {
              case 400:
                errorMsg = "提交数据格式不正确，请检查填写内容";
                break;
              case 401:
                errorMsg = "认证失败，请重新登录";
                break;
              case 500:
                errorMsg = "服务器内部错误，请联系管理员";
                break;
              default:
                errorMsg = error.response.data?.message || errorMsg;
            }
          } else if (error.request) {
            errorMsg = "无法连接到服务器，请检查网络连接";
          }
          
          this.$message.error(errorMsg);
          throw error;
        } finally {
          console.groupEnd();
        }
      } catch (error) {
        console.error("预约提交失败:", error);
        let errorMessage = "预约提交失败，请稍后再试";
        if (error.response) {
          errorMessage = error.response.data.message || errorMessage;
        }
        alert(errorMessage);
      }
    },
  },
};
</script>

<style lang="css" scoped>
@import "@/assets/css/appointmentform.css";

.appointment-success {
  padding: 20px;
  text-align: center;
  background: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0,0,0,0.1);
}

.success-image {
  margin-bottom: 20px;
}

.success-image img {
  width: 80px;
  height: 80px;
}

.appointment-success h3 {
  color: #4CAF50;
  margin-bottom: 20px;
  font-size: 24px;
}

.success-content {
  background: #f8f9fa;
  padding: 20px;
  border-radius: 8px;
  margin-bottom: 20px;
}

.success-content p {
  font-size: 18px;
  margin: 10px 0;
}

.appointment-details {
  margin-top: 20px;
  text-align: left;
  padding: 0 20px;
}

.appointment-details p {
  margin: 8px 0;
  font-size: 16px;
}

.countdown {
  margin-top: 15px;
  color: #666;
  font-size: 14px;
}

.close-button {
  background: #3498db;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
  margin-top: 20px;
}

.close-button:hover {
  background: #2980b9;
}
</style>