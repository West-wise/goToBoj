<template>
  <div class="container">
    <h1>
      <svg
        width="100%"
        height="100%"
        viewBox="0 0 500 100"
        xmlns="http://www.w3.org/2000/svg"
      >
        <text
          x="20"
          y="80"
          font-family= "'BMHANNA', sans-serif"
          font-weight="bold"
          font-size="70"
        >
          <tspan fill="#000">GoTo</tspan>
          <tspan fill="#0099FF">Baek</tspan>
          <tspan fill="#000">joon</tspan>
        </text>
      </svg>
    </h1>

    <div class="input-group">
      <input
        type="text"
        v-model="problemNumber"
        placeholder="문제 번호 입력"
        @keyup.enter="goToProblem"
      />
      <button @click="goToProblem">이동</button>
    </div>

    <!-- 주사위 아이콘 버튼 -->
    <div class="random-group">
      <button @click="goToRandomProblem" class="icon-button" title="랜덤 문제">
        <svg
          class="icon"
          xmlns="http://www.w3.org/2000/svg"
          width="28"
          height="28"
          viewBox="0 0 24 24"
          fill="currentColor"
        >
          <path
            d="M5 3C3.9 3 3 3.9 3 5v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2H5zm4.5 4c.8 0 1.5.7 1.5 1.5S10.3 10 9.5 10 8 9.3 8 8.5 8.7 7 9.5 7zm5 0c.8 0 1.5.7 1.5 1.5S15.3 10 14.5 10 13 9.3 13 8.5 13.7 7 14.5 7zM9.5 13c.8 0 1.5.7 1.5 1.5S10.3 16 9.5 16 8 15.3 8 14.5 8.7 13 9.5 13zm5 0c.8 0 1.5.7 1.5 1.5S15.3 16 14.5 16 13 15.3 13 14.5s.7-1.5 1.5-1.5z"
          />
        </svg>
      </button>
    </div>

    <!-- 오류 메시지 -->
    <div v-if="errorMessage" class="error-message">
      {{ errorMessage }}
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      problemNumber: "",
      errorMessage: "",
    };
  },
  methods: {
    goToProblem() {
      // ✅ null-check 및 공백 검증
      if (this.problemNumber == null || this.problemNumber.trim() === "") {
        this.showError("문제 번호를 입력해주세요");
        return;
      }

      const problemNum = parseInt(this.problemNumber.trim());

      if (isNaN(problemNum)) {
        this.showError("숫자만 입력해주세요");
        return;
      }

      if (problemNum < 1000 || problemNum > 33882) {
        this.showError("1000~33882 범위의 문제 번호를 입력하세요");
        return;
      }

      this.errorMessage = "";
      const problemUrl = `https://www.acmicpc.net/problem/${problemNum}`;

      try {
        chrome.tabs.create({ url: problemUrl }, (tab) => {
          if (chrome.runtime.lastError) {
            this.showError("탭 생성 실패: " + chrome.runtime.lastError.message);
          }
        });
      } catch (e) {
        this.showError("예외 발생: " + e.message);
      }
    },

    goToRandomProblem() {
      const randomNum = Math.floor(Math.random() * (33882 - 1000 + 1)) + 1000;
      const url = `https://www.acmicpc.net/problem/${randomNum}`;
      try {
        chrome.tabs.create({ url }, (tab) => {
          if (chrome.runtime.lastError) {
            this.showError("탭 생성 실패: " + chrome.runtime.lastError.message);
          }
        });
      } catch (e) {
        this.showError("예외 발생: " + e.message);
      }
    },

    showError(message) {
      this.errorMessage = message;
      setTimeout(() => {
        this.errorMessage = "";
      }, 3000);
    },
  },
};
</script>

<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap");

.container {
  /* 🔧 높이를 자동으로 조절 */
  height: 180px;
  width: 300px;
  padding: 16px;
  font-family: "BMHANNA", sans-serif;
}

h1 {
  font-size: 18px;
  margin-bottom: 8px;
  text-align: center;
}

.input-group {
  display: flex;
  gap: 8px;
  margin-bottom: 8px;
}

input {
  flex: 1;
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

button {
  padding: 8px 16px;
  background-color: #0078d7;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-weight: 600;
}

/* button:hover {
  background-color: #ffff;
} */

.random-group {
  text-align: center;
  margin-top: 6px;
}

/* 🎲 주사위 버튼 스타일 */
.icon-button {
  background: none;
  border: none;
  cursor: pointer;
  padding: 6px;
}

.icon {
  width: 28px;
  height: 28px;
  fill: #008cff;
  transition: transform 0.5s ease;
}

.icon-button:hover .icon {
  transform: rotate(200deg);
}

/* 에러 메시지 스타일 */
.error-message {
  color: #e53e3e;
  font-size: 12px;
  margin-top: 6px;
  padding: 4px 8px;
  background-color: #fff5f5;
  border-left: 4px solid #e53e3e;
  border-radius: 3px;
  animation: fadeIn 0.3s ease;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(-5px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@font-face {
  font-family: 'BMHANNA';
  src: url(/public/fonts/BMHANNAPro.ttf) format('woff');
  font-weight: 400;
  font-style: normal;
}
</style>
