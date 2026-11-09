<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Simple Calculator</title>
  <style>
    * {
      box-sizing: border-box;
    }

    body {
      min-height: 100vh;
      margin: 0;
      display: grid;
      place-items: center;
      background: #f1f3f5;
      font-family: Arial, sans-serif;
    }

    .calculator {
      width: min(320px, 92vw);
      padding: 20px;
      border-radius: 16px;
      background: #222;
      box-shadow: 0 8px 24px #0003;
    }

    #display {
      width: 100%;
      height: 64px;
      margin-bottom: 14px;
      padding: 10px;
      border: 0;
      border-radius: 8px;
      background: #f8f9fa;
      color: #222;
      font-size: 2rem;
      text-align: right;
    }

    .buttons {
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      gap: 8px;
    }

    button {
      min-height: 56px;
      border: 0;
      border-radius: 8px;
      background: #444;
      color: white;
      font-size: 1.2rem;
      cursor: pointer;
    }

    button:hover {
      background: #555;
    }

    button.operator {
      background: #f39c12;
    }

    button.operator:hover {
      background: #e58e0b;
    }

    button.action {
      background: #69737d;
    }

    button.equals {
      grid-column: span 2;
      background: #2ecc71;
    }

    button.equals:hover {
      background: #27ae60;
    }
  </style>
</head>
<body>
  <main class="calculator" aria-label="Calculator">
    <input id="display" type="text" value="0" aria-label="Display" readonly>

    <div class="buttons">
      <button class="action" data-action="clear">AC</button>
      <button class="action" data-action="delete">DEL</button>
      <button class="operator" data-operator="/">÷</button>
      <button class="operator" data-operator="*">×</button>

      <button data-digit="7">7</button>
      <button data-digit="8">8</button>
      <button data-digit="9">9</button>
      <button class="operator" data-operator="-">−</button>

      <button data-digit="4">4</button>
      <button data-digit="5">5</button>
      <button data-digit="6">6</button>
      <button class="operator" data-operator="+">+</button>

      <button data-digit="1">1</button>
      <button data-digit="2">2</button>
      <button data-digit="3">3</button>
      <button data-action="decimal">.</button>

      <button data-digit="0">0</button>
      <button class="equals" data-action="equals">=</button>
    </div>
  </main>

  <script>
    const display = document.querySelector("#display");
    let currentValue = "0";
    let storedValue = null;
    let pendingOperator = null;
    let startNewNumber = false;

    function updateDisplay() {
      display.value = currentValue;
    }

    function enterDigit(digit) {
      if (startNewNumber || currentValue === "0" || currentValue === "Error") {
        currentValue = digit;
        startNewNumber = false;
      } else {
        currentValue += digit;
      }
      updateDisplay();
    }

    function chooseOperator(operator) {
      if (currentValue === "Error") return;

      if (pendingOperator && !startNewNumber) {
        calculate();
      }

      storedValue = Number(currentValue);
      pendingOperator = operator;
      startNewNumber = true;
    }

    function calculate() {
      if (pendingOperator === null || storedValue === null) return;

      const first = storedValue;
      const second = Number(currentValue);
      let result;

      switch (pendingOperator) {
        case "+": result = first + second; break;
        case "-": result = first - second; break;
        case "*": result = first * second; break;
        case "/":
          if (second === 0) {
            currentValue = "Error";
            storedValue = null;
            pendingOperator = null;
            startNewNumber = true;
            updateDisplay();
            return;
          }
          result = first / second;
          break;
      }

      currentValue = String(Number(result.toFixed(10)));
      storedValue = null;
      pendingOperator = null;
      startNewNumber = true;
      updateDisplay();
    }

    document.querySelector(".buttons").addEventListener("click", (event) => {
      const button = event.target.closest("button");
      if (!button) return;

      if (button.dataset.digit !== undefined) {
        enterDigit(button.dataset.digit);
      } else if (button.dataset.operator) {
        chooseOperator(button.dataset.operator);
      } else {
        switch (button.dataset.action) {
          case "clear":
            currentValue = "0";
            storedValue = null;
            pendingOperator = null;
            startNewNumber = false;
            updateDisplay();
            break;
          case "delete":
            if (currentValue !== "Error") {
              currentValue = currentValue.length > 1
                ? currentValue.slice(0, -1)
                : "0";
              updateDisplay();
            }
            break;
          case "decimal":
            if (startNewNumber || currentValue === "Error") {
              currentValue = "0.";
              startNewNumber = false;
            } else if (!currentValue.includes(".")) {
              currentValue += ".";
            }
            updateDisplay();
            break;
          case "equals":
            calculate();
            break;
        }
      }
    });
  </script>
</body>
</html>
