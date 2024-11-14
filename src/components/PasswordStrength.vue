<template>
  <div class="password-checker">
    <h2>Password Strength Checker</h2>
    <input
      v-model="username"
      type="text"
      placeholder="Enter your username"
      @input="checkPassword"
    />
    <div>
      <div class="password-input-container">
        <input
          v-model="password"
          :type="showPassword ? 'text' : 'password'"
          placeholder="Enter your password"
          @input="checkPassword"
        />
        <div class="show-password-container">
          <input type="checkbox" v-model="showPassword" />
          <span>Show Password</span>
        </div>
      </div>
    </div>
    <p>Score: {{ score }}/10 - {{ strengthText }}</p>

    <div class="feedback">
      <div v-if="errors.length">
        <h3>Errors</h3>
        <ul>
          <li v-for="(item, index) in errors" :key="index">{{ item }}</li>
        </ul>
      </div>

      <div v-if="suggestions.length">
        <h3>Suggestions</h3>
        <ul>
          <li v-for="(item, index) in suggestions" :key="index">{{ item }}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      username: "",
      password: "",
      commonPasswords: ["password", "12345", "qwerty", "admin", "letmein"],
      score: 5,
      strengthText: "Weak",
      errors: [],
      suggestions: [],
      showPassword: false,
    };
  },
  methods: {
    checkPassword() {
      this.errors = [];
      this.suggestions = [];
      this.score = 5;

      if (!this.password) {
        this.strengthText = "No password entered";
        return;
      }

      this.checkLength();

      if (this.password.length >= 8) {
        this.checkComplexity();
        this.checkCommonPatterns();
        this.checkUniqueness();
      }

      this.updateStrengthText();
    },
    checkLength() {
      if (this.password.length >= 8) {
        this.score += 1;
      } else {
        this.errors.push("Password should be at least 8 characters long.");
        this.suggestions.push(
          "Consider increasing the password length to at least 8 characters."
        );
      }
    },
    checkComplexity() {
      const hasLower = /[a-z]/.test(this.password);
      const hasUpper = /[A-Z]/.test(this.password);
      const hasDigit = /\d/.test(this.password);
      const hasSpecial = /[!@#$%^&*(),.?":{}|<>]/.test(this.password);

      if (hasLower) this.score += 1;
      else {
        this.errors.push(
          "Password should include at least one lowercase letter."
        );
        this.suggestions.push("Consider adding at least one lowercase letter.");
      }

      if (hasUpper) this.score += 1;
      else {
        this.errors.push(
          "Password should include at least one uppercase letter."
        );
        this.suggestions.push("Consider adding at least one uppercase letter.");
      }
      if (hasDigit) this.score += 1;
      else {
        this.errors.push("Password should include at least one digit.");
        this.suggestions.push(
          "Consider adding at least one digit (e.g., 0-9)."
        );
      }
      if (hasSpecial) this.score += 1;
      else {
        this.errors.push(
          "Password should include at least one special character."
        );
        this.suggestions.push(
          "Try adding a special character, such as !, @, #, $, etc."
        );
      }
    },
    checkCommonPatterns() {
      const passwordLower = this.password.toLowerCase();
      if (this.commonPasswords.some((word) => passwordLower.includes(word))) {
        this.errors.push(
          "Password should not contain common words or phrases."
        );
        this.suggestions.push(
          "Avoid using common passwords like 'password', '12345', or 'qwerty'."
        );
        this.score -= 1;
      }
    },
    checkUniqueness() {
      if (
        this.username &&
        this.password.toLowerCase() === this.username.toLowerCase()
      ) {
        this.errors.push("Password should not be identical to the username.");
        this.suggestions.push(
          "Make sure your password is different from your username."
        );
        this.score -= 1;
      }
    },
    updateStrengthText() {
      if (this.score > 10) {
        this.score = 10;
      }

      if (this.score === 10) {
        this.strengthText = "Strong";
      } else if (this.score >= 8) {
        this.strengthText = "Good";
      } else if (this.score >= 6) {
        this.strengthText = "Moderate";
      } else {
        this.strengthText = "Weak";
      }
    },
  },
};
</script>

<style scoped>
.password-checker {
  max-width: 400px;
  margin: 0 auto;
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 8px;
  background-color: #f9f9f9;
}

input {
  padding: 8px;
  margin: 10px 0;
  border: 1px solid #ccc;
  border-radius: 4px;
}
input[type="text"],
input[type="password"] {
  width: 100%;
}
span {
  margin-left: 5px;
}
p {
  font-weight: bold;
}

.feedback {
  margin-top: 20px;
}

h3 {
  color: #e74c3c;
}

ul {
  list-style-type: none;
  padding: 0;
}

ul li {
  margin: 5px 0;
}

.errors li {
  color: red;
}

.suggestions li {
  color: green;
}

.password-input-container {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  width: 100%;
}

.show-password-container {
  display: flex;
  align-items: center;
  margin-top: 10px;
}

label {
  font-size: 14px;
  display: inline-flex;
  margin-left: 5px;
}
</style>
