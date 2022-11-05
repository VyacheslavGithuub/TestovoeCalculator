<script>
  import CalculatorUI from "./CalculatorUI.svelte";

  let calculatedField = [];
  let result = null;

  // Проверка на знак / число
  $: lastIsNumber = calculatedField[calculatedField.length - 1] && !isNaN(calculatedField[calculatedField.length - 1]);

  // Обрабатывать вычисления
  function handleComputing(o) {
    const operations = {
      "+": (x, y) => (x + y).toPrecision(9) / 1,
      "-": (x, y) => (x - y).toPrecision(9) / 1,
      "/": (x, y) => (x / y).toPrecision(9) / 1,
      x: (x, y) => (x * y).toPrecision(9) / 1,
    };
    
    if (calculatedField[calculatedField.length - 3] === "=") {
      calculatedField = [result, o];
    }

    let lengArr = calculatedField.length;
    let prevOperand = calculatedField[lengArr - 3];
    
    if (lengArr > 3 && lengArr % 2 === 0){ 
    result = operations[prevOperand](Number(result), Number(calculatedField[lengArr - 2]));
    }
  }
// Функция для чисел
  function handleNumber(e) {
    if (lastIsNumber) {
      // Если ноль первый то мы не можем добавить еще нулей
      // Если ноль первый при нажатии на другую цифру заменяем ноль на цифру
      if (
        calculatedField[calculatedField.length - 1].indexOf(".") === -1 &&
        calculatedField[calculatedField.length - 1] === "0"
      ) {
        calculatedField[calculatedField.length - 1] = e.target.innerText;
         // Иначе заполняем calculatedField[]
      } else {
        calculatedField[calculatedField.length - 1] =
          calculatedField[calculatedField.length - 1] + e.target.innerText;
      }
      // Если последний элемент знак
    } else {
      if (calculatedField[calculatedField.length - 1] === "=") {
        handleAC();
      }
      calculatedField.push(e.target.innerText);
    }
    // при первом операнде "result" отслеживает значение в calculatedField[]
    if (calculatedField.length === 1) {
      result = parseFloat(calculatedField[0]);
    }
  // Необходимо для синхронной работы
    calculatedField = calculatedField;
  }

  // обрабатывать операнд
  function handleOperand(e) {
    if (calculatedField.length) {
      if (isNaN(calculatedField[calculatedField.length - 1])) {
        // Если нажали равно то  приравниваем к result
        if (calculatedField[calculatedField.length - 1] === "=") {
          calculatedField = [result, e.target.innerText];
        } else {
          // если последний элемент это знак, при нажатии на другой знак, то первый знак заменится на второй.
          calculatedField[calculatedField.length - 1] = e.target.innerText;
        }
      } else {
        calculatedField.push(e.target.innerText);
        handleComputing(e.target.innerText);
      }
    } else {
      // Обрабатываем первое нажатие символов
      calculatedField.push(result);
      calculatedField.push(e.target.innerText);
    }
    // Необходимо для синхронной работы
    calculatedField = calculatedField;
  }

 // обрабатывать Равно
  function handleEqual() {
    if (calculatedField.length > 2) {
      if (calculatedField[calculatedField.length - 1] !== "=") {
        if (lastIsNumber) {
          calculatedField.push("=");
          setTimeout(()=>{
            calculatedField = []
          calculatedField.push(result)
          },0)
        } else {
          calculatedField[calculatedField.length - 1] = "=";
        }
      } else {
        calculatedField = [
          result,
          calculatedField[calculatedField.length - 3],
          calculatedField[calculatedField.length - 2],
          "="
        ];
      }
      handleComputing();
      calculatedField = calculatedField;
    }
  }

  // обрабатывать десятичные
  function handleDecimal() {
    let last = calculatedField[calculatedField.length - 1];
    if (isNaN(last)) {
      if (last === "=") {
        handleAC();
      }
      calculatedField.push("0.");
      calculatedField = calculatedField;
    } else if (last.indexOf(".") === -1) {
      calculatedField[calculatedField.length - 1] = calculatedField[calculatedField.length - 1] + ".";
    }
  }

  // инвертирование знака числа
  function handleInvertSign() {
    if (lastIsNumber) {
      calculatedField[calculatedField.length - 1] = 0 - calculatedField[calculatedField.length - 1];
      if (calculatedField.length === 1) {
        result = calculatedField[calculatedField.length - 1];
      }
    } else if (calculatedField[calculatedField.length - 1] === "=") {
      result = 0 - result;
      calculatedField = [result];
    }
  }

  // Процент от последнего числа
  function handlePercent() {
    if (lastIsNumber) {
      calculatedField[calculatedField.length - 1] =
        calculatedField[calculatedField.length - 1] / 100;
    } else {
      calculatedField[calculatedField.length - 2] =
        calculatedField[calculatedField.length - 2] / 100;
      calculatedField.splice(-1, 1);
    }
  }
  // All Clean
  function handleAC() {
    calculatedField = [];
    result = 0;
  }
  // Clear
  function handleClear() {
    calculatedField[calculatedField.length - 1] = calculatedField[calculatedField.length - 1].slice(0, -1);
    if (calculatedField[calculatedField.length - 1].length === 0) {
      calculatedField.pop()
    }  
  }
    
  // обрабатывать нажатие клавиши
  function handleKeypress(e) {
    let k = { target: { innerText: e.key } };
    if (e.key === "*") {
      k.target.innerText = "x";
    }

    if (!isNaN(e.key)) {
      handleNumber(k);
    } else if (["+", "-", "/", "*"].indexOf(e.key) > -1) {
      handleOperand(k);
    } else {
      switch (e.key) {
        case "Enter":
          e.preventDefault();
          handleEqual();
          break;
        case "Backspace":
          handleClear();
          break;
        case "Delete":
          handleAC();
          break;
        case ".":
          handleDecimal();
          break;
      }
    }
  }

  let buttons = [
    {
      value: "AC",
      func: handleAC,
      id: "AC",
    },
    {
      value: "+/-",
      func: handleInvertSign,
      id: "neg",
    },
    {
      value: "%",
      func: handlePercent,
      id: "procent",
    },
    {
      value: "<=",
      func: handleClear,
      id: "clear",
    },
    {
      value: "7",
      func: handleNumber,
      id: "seven",
    },
    {
      value: "8",
      func: handleNumber,
      id: "eight",
    },
    {
      value: "9",
      func: handleNumber,
      id: "nine",
    },
    {
      value: "/",
      func: handleOperand,
      id: "divide",
    },
    {
      value: "4",
      func: handleNumber,
      id: "four",
    },
    {
      value: "5",
      func: handleNumber,
      id: "five",
    },
    {
      value: "6",
      func: handleNumber,
      id: "six",
    },
    {
      value: "x",
      func: handleOperand,
      id: "multiply",
    },
    {
      value: "1",
      func: handleNumber,
      id: "one",
    },
    {
      value: "2",
      func: handleNumber,
      id: "two",
    },
    {
      value: "3",
      func: handleNumber,
      id: "three",
    },
    {
      value: "-",
      func: handleOperand,
      id: "minus",
    },
    {
      value: ".",
      func: handleDecimal,
      id: "decimal",
    },
    {
      value: "0",
      func: handleNumber,
      id: "zero",
    },
    {
      value: "=",
      func: handleEqual,
      id: "equal",
    },
    {
      value: "+",
      func: handleOperand,
      id: "plus",
    },
  ];
</script>

<svelte:body on:keydown={handleKeypress} />

<CalculatorUI {buttons} {calculatedField} />