# 用 Copilot 编写测试

通过示例讲解生成和运行单元测试、使用测试命令及编写集成测试的流程，用于提高覆盖率并验证实际行为。

- 来源：GitHub／Copilot
- 资料类型：存档文档
- 文档时间：官方文件提交（截至抓取）：[2026-09-30](<https://github.com/github/docs/commit/d9fd377ea956f59385ac8cb2034ba5c613b6fd85>)；发表/更新：官方未公开；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://docs.github.com/en/copilot/tutorials/write-tests>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/用 Copilot 编写测试.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 使用 GitHub Copilot 编写测试

使用Copilot生成单元和集成测试，并帮助提高代码质量。

## 简介

GitHub Copilot可以帮助您快速开发测试并提高生产力。在本文中，我们将演示如何使用 Copilot 编写单元测试和集成测试。虽然Copilot在生成基本功能的测试时表现良好，但复杂的场景需要更详细的提示和策略。本文将介绍使用 Copilot 分解任务并验证代码正确性的实际示例。

## 先决条件

在开始之前，您必须具备以下条件：

* [GitHub Copilot订阅计划](<https://docs.github.com/en/copilot/get-started/plans>)。
* Visual Studio、Visual Studio Code 或任何 JetBrains IDE。
* [GitHub Copilot 扩展 ](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension>) 安装在您的 IDE 中。

## 使用 Copilot 聊天编写单元测试

在本节中，我们将探讨如何使用 GitHub Copilot Chat 为 Python 类生成单元测试。此示例演示如何使用 Copilot 为 `BankAccount` 等类创建单元测试。我们将向您展示如何提示 Copilot 生成测试、执行测试并验证结果。

### 示例类：`BankAccount`

让我们从一个类 `BankAccount` 开始，它包含存款、取款和获取帐户余额的方法。在 GitHub 存储库中创建一个新文件 `bank_account.py`，并在 Python 中添加以下 `BankAccount` 类。

```python
class BankAccount:
    def __init__(self, initial_balance=0):
        if initial_balance < 0:
            raise ValueError("Initial balance cannot be negative.")
        self.balance = initial_balance

    def deposit(self, amount):
        if amount <= 0:
            raise ValueError("Deposit amount must be positive.")
        self.balance += amount

    def withdraw(self, amount):
        if amount <= 0:
            raise ValueError("Withdrawal amount must be positive.")
        if amount > self.balance:
            raise ValueError("Cannot withdraw more than the current balance.")
        self.balance -= amount

    def get_balance(self):
        return self.balance
```

### 使用 Copilot 聊天生成单元测试

您可以提示 Copilot 并请求编写涵盖一系列场景的测试。具体说明您的测试要求以获得最佳结果。以下是可用于为 `BankAccount` 类生成单元测试的示例提示。

**提示：** *为Python中的BankAccount()函数开发一套全面的单元测试。编写涵盖广泛场景的多种测试方法，包括边缘情况、异常处理和数据验证。*

这个提示做了一些重要的事情：

* **范围：**它明确要求提供一套全面的单元测试，以便Copilot生成的不仅仅是基本测试，还涵盖了各种潜在的用例。
* **场景：** 提示指定测试应包括：
  * **边缘情况：**边界条件测试（例如存款或取款的负值、取款超过可用余额）。
  * **异常处理：**测试发生某些无效操作时应引发的错误（例如，提取的金额超过可用余额、存入负金额）。
  * **数据验证：**确保方法正确验证和处理输入。

Copilot会给您如下的回复。

```python
import unittest
from bank_account import BankAccount

class TestBankAccount(unittest.TestCase):
    def setUp(self):
        self.account = BankAccount()

    def test_initial_balance(self):
        self.assertEqual(self.account.get_balance(), 0)

    def test_deposit_positive_amount(self):
        self.account.deposit(100)
        self.assertEqual(self.account.get_balance(), 100)

    def test_withdraw_within_balance(self):
        self.account.deposit(100)
        self.account.withdraw(50)
        self.assertEqual(self.account.get_balance(), 50)

    def test_deposit_negative_amount_raises_error(self):
        with self.assertRaises(ValueError):
            self.account.deposit(-100)

    def test_withdraw_negative_amount_raises_error(self):
        with self.assertRaises(ValueError):
            self.account.withdraw(-50)

    def test_withdraw_more_than_balance_raises_error(self):
        self.account.deposit(100)
        with self.assertRaises(ValueError):
            self.account.withdraw(200)

    def test_initial_balance_negative_raises_error(self):
        with self.assertRaises(ValueError):
            BankAccount(-100)

if __name__ == '__main__':
    unittest.main()
```

### 运行测试用例

Copilot 生成的测试套件包括提示中指定的每个场景的测试用例。 `BankAccount` 类中的每个函数都针对一系列条件进行测试，包括存款和取款等有效操作，以及负金额和尝试提取超过可用余额等边缘情况。

一旦 Copilot 生成了令您满意的测试套件，请将代码添加到新文件 `test_bank_account.py` 中。您可以询问它如何运行测试。

**提示：** *“如何使用单元测试框架在Python中运行这些单元测试？”*

Copilot 将为您提供以下 bash 命令。

```bash
python -m unittest test_bank_account.py
```

运行测试后，您将在终端或 IDE 中看到输出。如果所有测试都通过，您可以确信您的 `BankAccount` 类正在按预期工作。

#### 斜线命令

此外，您可以使用 `/tests` 斜杠命令提示 Copilot 编写全套单元测试。确保您在 IDE 的当前选项卡上打开该文件，并且 Copilot 将为该文件生成单元测试。 Copilot 生成的测试可能无法涵盖所有​​场景，因此您应该始终检查生成的代码并添加可能需要的任何其他测试。

> \[!TIP] 如果您要求 Copilot 为单元测试尚未涵盖的代码文件编写测试，您可以通过在编辑器的相邻选项卡中打开一个或多个现有测试文件来为 Copilot 提供有用的上下文。 Copilot 将能够看到您使用的测试框架，并且更有可能编写与您现有测试一致的测试。

Copilot 将生成如下所示的单元测试套件。

```python
import unittest
from bank_account import BankAccount

class TestBankAccount(unittest.TestCase):
    def setUp(self):
        self.account = BankAccount()

    def test_initial_balance(self):
        self.assertEqual(self.account.get_balance(), 0)
```

## 使用 Copilot 编写集成测试

集成测试对于确保系统的各个组件在组合时正常工作至关重要。在本节中，我们将扩展 `BankAccount` 类以包含与外部服务 `NotificationSystem` 的交互，并使用模拟来测试系统的行为，而无需真正的连接。集成测试的目标是验证 `BankAccount` 类和 `NotificationSystem` 服务之间的交互，确保它们正确协同工作。

### 示例类：`BankAccount` 具有通知服务

让我们更新 `BankAccount` 类以包含与外部服务的交互，例如向用户发送通知的 `NotificationSystem`。 `NotificationSystem` 表示需要测试的集成。

使用以下代码片段更新 `bank_account.py` 文件中的 `BankAccount` 类。

```python
class BankAccount:
    def __init__(self, initial_balance=0, notification_system=None):
        if initial_balance < 0:
            raise ValueError("Initial balance cannot be negative.")
        self.balance = initial_balance
        self.notification_system = notification_system

    def deposit(self, amount):
        if amount <= 0:
            raise ValueError("Deposit amount must be positive.")
        self.balance += amount
        if self.notification_system:
            self.notification_system.notify(f"Deposited {amount}, new balance: {self.balance}")

    def withdraw(self, amount):
        if amount <= 0:
            raise ValueError("Withdrawal amount must be positive.")
        if amount > self.balance:
            raise ValueError("Cannot withdraw more than the current balance.")
        self.balance -= amount

        if self.notification_system:
            self.notification_system.notify(f"Withdrew {amount}, new balance: {self.balance}")

    def get_balance(self):
        return self.balance
```

在这里，我们将把 Copilot 的请求分解为更小、更易于管理的部分，以便为 `BankAccount` 类编写集成测试。这将有助于 Copilot 生成更准确和相关的测试。

**提示：** *"为`BankAccount`类中的`deposit`函数编写集成测试。使用mocks模拟`NotificationSystem`并验证在存款。”*

这个提示做了一些重要的事情：

* **范围：**它指定集成测试，重点关注`deposit`函数和`NotificationSystem`之间的交互，而不仅仅是单元测试。
* **Mocks：**它明确要求使用mock来模拟`NotificationSystem`，确保与外部系统的交互在不依赖其实际实现的情况下进行测试。
* **验证：**该提示强调验证存款后是否正确调用`NotificationSystem`，确保组件之间的集成按预期进行。
* **特异性：** 提示清楚地说明了要测试的方法（`deposit`）和类（`BankAccount`）。

> \[!TIP] 如果 Copilot 生成无效测试，请提供要测试的函数的输入和输出示例。这将有助于 Copilot 评估函数的预期行为。

Copilot 将生成如下所示的测试套件。

```python
import unittest
from unittest.mock import Mock
from bank_account import BankAccount

class TestBankAccountIntegration(unittest.TestCase):
    def setUp(self):
        self.notification_system = Mock()

    def test_deposit_with_notification(self):
        account = BankAccount(initial_balance=100, notification_system=self.notification_system)
        account.deposit(50)
        self.assertEqual(account.get_balance(), 150)
        self.notification_system.notify.assert_called_once_with("Deposited 50, new balance: 150")

if __name__ == '__main__':
    unittest.main()
```

将生成的代码添加到新文件`test_bank_account_integration.py`中。

### 改进测试用例

上面的提示生成了一个测试用例，用于验证在进行有效存款时调用 `NotificationSystem`。但是，它不包括存款过程中出现错误的情况。在这些情况下，不应调用 `NotificationSystem`。我们需要添加一个测试用例来处理无效存款并确保通知系统不会被触发。

**提示：** *“添加无效存款金额的测试用例，以验证该函数引发正确的异常并且未调用`NotificationService`。”*

Copilot 将生成如下测试用例。

```python
    def test_deposit_negative_amount_raises_error(self):
        account = BankAccount(initial_balance=100, notification_system=self.notification_system)
        with self.assertRaises(ValueError):
            account.deposit(0)
        self.notification_system.notify.assert_not_called()
```

### 询问需要改进的地方

现在我们已经编写了测试用例来验证存款的集成功能，这是在测试套件中寻找改进的绝佳机会。虽然当前的测试有效，但我们可以提示 Copilot 评估代码覆盖率并提出改进的建议。

**提示：** *“应包括哪些附加测试以确保 `BankAccount` 类和 `NotificationSystem` 之间的集成完全覆盖？”*

用这个问题提示Copilot可以帮助您识别可能被忽略的缺失测试用例。
在这种情况下，虽然我们测试了有效和无效存款，但我们尚未涵盖取款功能。

Copilot 将生成更新的测试套件，如下所示。



<summary>单击展开完整生成的代码示例</summary>

```python
import unittest
from unittest.mock import Mock
from bank_account import BankAccount

class TestBankAccountIntegration(unittest.TestCase):
    def setUp(self):
        self.notification_system = Mock()

    def test_deposit_with_notification(self):
        account = BankAccount(initial_balance=100, notification_system=self.notification_system)
        account.deposit(50)
        self.assertEqual(account.get_balance(), 150)
        self.notification_system.notify.assert_called_once_with("Deposited 50, new balance: 150")

    def test_deposit_negative_amount_raises_error(self):
        account = BankAccount(initial_balance=100, notification_system=self.notification_system)
        with self.assertRaises(ValueError):
            account.deposit(-50)
        self.notification_system.notify.assert_not_called()

    def test_deposit_zero_amount_raises_error(self):
        account = BankAccount(initial_balance=100, notification_system=self.notification_system)
        with self.assertRaises(ValueError):
            account.deposit(0)
        self.notification_system.notify.assert_not_called()

    def test_withdraw_with_notification(self):
        account = BankAccount(initial_balance=100, notification_system=self.notification_system)
        account.withdraw(30)
        self.assertEqual(account.get_balance(), 70)
        self.notification_system.notify.assert_called_once_with("Withdrew 30, new balance: 70")

    def test_withdraw_exceeding_balance_raises_error(self):
        account = BankAccount(initial_balance=100, notification_system=self.notification_system)
        with self.assertRaises(ValueError):
            account.withdraw(150)
        self.notification_system.notify.assert_not_called()

    def test_withdraw_negative_amount_raises_error(self):
        account = BankAccount(initial_balance=100, notification_system=self.notification_system)
        with self.assertRaises(ValueError):
            account.withdraw(-30)
        self.notification_system.notify.assert_not_called()

    def test_withdraw_zero_amount_raises_error(self):
        account = BankAccount(initial_balance=100, notification_system=self.notification_system)
        with self.assertRaises(ValueError):
            account.withdraw(0)
        self.notification_system.notify.assert_not_called()

    def test_initial_negative_balance_raises_error(self):
        with self.assertRaises(ValueError):
            BankAccount(initial_balance=-100, notification_system=self.notification_system)

if __name__ == '__main__':
    unittest.main()
```




一旦 Copilot 生成了令您满意的测试套件，请使用以下命令运行测试以验证结果。

```bash
python -m unittest test_bank_account_integration.py
```

## 使用 Copilot 空间改进测试建议

Copilot 空间是一项功能，允许您使用 Copilot 组织和共享特定于任务的上下文。这可以帮助提高您收到的建议的相关性。通过向 Copilot 提供有关您的项目的更多背景信息，您可以获得更好的测试建议。

例如，您可以创建一个包含以下内容的空间：

* 您正在测试的模块（例如 `payments.js`）
* 当前的测试套件（如`payments.test.js`）
* 测试覆盖率报告或有关缺失内容的注释

在该空间中，您可以提出 Copilot 问题，例如：

> 根据`payments.js`中的逻辑，`payments.test.js`中缺少哪些测试用例？

或者：

> 按照现有测试套件中的结构，为`refund.js`中的退款逻辑编写一个单元测试。

有关使用 Copilot 空间的更多信息，请参阅[关于 GitHub Copilot 空间](<https://docs.github.com/en/copilot/concepts/context/spaces>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Writing tests with GitHub Copilot

Use Copilot to generate unit and integration tests, and help improve code quality.

## Introduction

GitHub Copilot can assist you in developing tests quickly and improving productivity. In this article, we’ll demonstrate how you can use Copilot to write both unit and integration tests. While Copilot performs well when generating tests for basic functions, complex scenarios require more detailed prompts and strategies. This article will walk through practical examples of using Copilot to break down tasks and verify code correctness.

## Prerequisites

Before getting started you must have the following:

* A [GitHub Copilot subscription plan](<https://docs.github.com/en/copilot/get-started/plans>).
* Visual Studio, Visual Studio Code, or any JetBrains IDE.
* The [GitHub Copilot extension](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension>) installed in your IDE.

## Writing unit tests with Copilot Chat

In this section, we’ll explore how to use GitHub Copilot Chat to generate unit tests for a Python class. This example demonstrates how you can use Copilot to create unit tests for a class like `BankAccount`. We will show you how to prompt Copilot to generate tests, execute them, and verify the results.

### Example class: `BankAccount`

Let’s start with a class `BankAccount` that contains methods for depositing, withdrawing, and getting the balance of an account. Create a new file `bank_account.py` in a GitHub repository and add the following `BankAccount` class in Python.

```python
class BankAccount:
    def __init__(self, initial_balance=0):
        if initial_balance < 0:
            raise ValueError("Initial balance cannot be negative.")
        self.balance = initial_balance

    def deposit(self, amount):
        if amount <= 0:
            raise ValueError("Deposit amount must be positive.")
        self.balance += amount

    def withdraw(self, amount):
        if amount <= 0:
            raise ValueError("Withdrawal amount must be positive.")
        if amount > self.balance:
            raise ValueError("Cannot withdraw more than the current balance.")
        self.balance -= amount

    def get_balance(self):
        return self.balance
```

### Generating unit tests with Copilot Chat

You can prompt Copilot with a request to write tests that cover a range of scenarios. Be specific with your test requirements to get the best results. The following is an example prompt you can use to generate unit tests for the `BankAccount` class.

**Prompt:** *Develop a comprehensive suite of unit tests for the BankAccount() function in Python. Write multiple test methods that cover a wide range of scenarios, including edge cases, exception handling, and data validation.*

This prompt does a few important things:

* **Scope:** It explicitly asks for a comprehensive suite of unit tests, so that Copilot generates more than just basic tests, covering a variety of potential use cases.
* **Scenarios:** The prompt specifies that the tests should include:
  * **Edge cases:** Tests for boundary conditions (e.g., negative values for deposits or withdrawals, withdrawing more than the available balance).
  * **Exception handling:** Tests for errors that should be raised when certain invalid actions occur (e.g., withdrawing more than the available balance, depositing negative amounts).
  * **Data validation:** Ensures that inputs are correctly validated and handled by the methods.

Copilot will give you a response like the following.

```python
import unittest
from bank_account import BankAccount

class TestBankAccount(unittest.TestCase):
    def setUp(self):
        self.account = BankAccount()

    def test_initial_balance(self):
        self.assertEqual(self.account.get_balance(), 0)

    def test_deposit_positive_amount(self):
        self.account.deposit(100)
        self.assertEqual(self.account.get_balance(), 100)

    def test_withdraw_within_balance(self):
        self.account.deposit(100)
        self.account.withdraw(50)
        self.assertEqual(self.account.get_balance(), 50)

    def test_deposit_negative_amount_raises_error(self):
        with self.assertRaises(ValueError):
            self.account.deposit(-100)

    def test_withdraw_negative_amount_raises_error(self):
        with self.assertRaises(ValueError):
            self.account.withdraw(-50)

    def test_withdraw_more_than_balance_raises_error(self):
        self.account.deposit(100)
        with self.assertRaises(ValueError):
            self.account.withdraw(200)

    def test_initial_balance_negative_raises_error(self):
        with self.assertRaises(ValueError):
            BankAccount(-100)

if __name__ == '__main__':
    unittest.main()
```

### Running the tests cases

The test suite generated by Copilot includes a test case for each scenario specified in the prompt. Each function in the `BankAccount` class is tested for a range of conditions, including valid operations like deposits and withdrawals, as well as edge cases such as negative amounts and attempts to withdraw more than the available balance.

Once Copilot has generated the test suite to your satisfaction, add the code to a new file `test_bank_account.py`. You can ask it how to run the tests.

**Prompt:** *"How do I run these unit tests in Python using the unittest framework?"*

Copilot will give you the following bash command.

```bash
python -m unittest test_bank_account.py
```

After running the tests, you will see the output in your terminal or IDE. If all tests pass, you can be confident that your `BankAccount` class is working as expected.

#### Slash command

Additionally, you can prompt Copilot to write a full suite of unit tests with the `/tests` slash command. Ensure that you have the file open on the current tab of your IDE and Copilot will generate unit tests for that file. The tests that Copilot generates may not cover all scenarios, so you should always review the generated code and add any additional tests that may be necessary.

> \[!TIP] If you ask Copilot to write tests for a code file that is not already covered by unit tests, you can provide Copilot with useful context by opening one or more existing test files in adjacent tabs in your editor. Copilot will be able to see the testing framework you use and will be more likely to write a test that is consistent with your existing tests.

Copilot will generate a unit test suite such as the following.

```python
import unittest
from bank_account import BankAccount

class TestBankAccount(unittest.TestCase):
    def setUp(self):
        self.account = BankAccount()

    def test_initial_balance(self):
        self.assertEqual(self.account.get_balance(), 0)
```

## Writing integration tests with Copilot

Integration tests are essential for ensuring that the various components of your system work correctly when combined. In this section, we’ll extend our `BankAccount` class to include interactions with an external service `NotificationSystem` and use mocks to test the system’s behavior without needing real connections. The goal of the integration tests is to verify the interaction between the `BankAccount` class and the `NotificationSystem` services, ensuring that they work together correctly.

### Example class: `BankAccount` with notification services

Let's update the `BankAccount` class to include interactions with an external service such as a `NotificationSystem` that sends notifications to users. `NotificationSystem` represents the integration that would need to be tested.

Update the `BankAccount` class in the `bank_account.py` file with the following code snippet.

```python
class BankAccount:
    def __init__(self, initial_balance=0, notification_system=None):
        if initial_balance < 0:
            raise ValueError("Initial balance cannot be negative.")
        self.balance = initial_balance
        self.notification_system = notification_system

    def deposit(self, amount):
        if amount <= 0:
            raise ValueError("Deposit amount must be positive.")
        self.balance += amount
        if self.notification_system:
            self.notification_system.notify(f"Deposited {amount}, new balance: {self.balance}")

    def withdraw(self, amount):
        if amount <= 0:
            raise ValueError("Withdrawal amount must be positive.")
        if amount > self.balance:
            raise ValueError("Cannot withdraw more than the current balance.")
        self.balance -= amount

        if self.notification_system:
            self.notification_system.notify(f"Withdrew {amount}, new balance: {self.balance}")

    def get_balance(self):
        return self.balance
```

Here we'll break down our request for Copilot to write integration tests for the `BankAccount` class into smaller, more manageable pieces. This will help Copilot generate more accurate and relevant tests.

**Prompt:** *"Write integration tests for the `deposit` function in the `BankAccount` class. Use mocks to simulate the `NotificationSystem` and verify that it is called correctly after a deposit."*

This prompt does a few important things:

* **Scope:** It specifies integration tests, focusing on the interaction between the `deposit` function and the `NotificationSystem`, rather than just unit tests.
* **Mocks:** It explicitly asks for the use of mocks to simulate the `NotificationSystem`, ensuring that the interaction with external systems is tested without relying on their actual implementation.
* **Verification:** The prompt emphasizes verifying that the `NotificationSystem` is called correctly after a deposit, ensuring that the integration between the components works as expected.
* **Specificity:** The prompt clearly states the method (`deposit`) and the class (`BankAccount`) to be tested.

> \[!TIP] If Copilot is producing invalid tests, provide examples of inputs and outputs for the function you want to test. This will help Copilot evaluate the expected behavior of the function.

Copilot will generate a test suite like the following.

```python
import unittest
from unittest.mock import Mock
from bank_account import BankAccount

class TestBankAccountIntegration(unittest.TestCase):
    def setUp(self):
        self.notification_system = Mock()

    def test_deposit_with_notification(self):
        account = BankAccount(initial_balance=100, notification_system=self.notification_system)
        account.deposit(50)
        self.assertEqual(account.get_balance(), 150)
        self.notification_system.notify.assert_called_once_with("Deposited 50, new balance: 150")

if __name__ == '__main__':
    unittest.main()
```

Add the generated code to a new file `test_bank_account_integration.py`.

### Improving on the test cases

The prompt above generated a single test case that verifies the `NotificationSystem` is called when a valid deposit is made. However, it doesn't cover cases where an error is raised during the deposit. In those scenarios, the `NotificationSystem` should not be called. We need to add a test case that handles invalid deposits and ensure the notification system is not triggered.

**Prompt:** *"Add a test case for invalid deposit amounts to verify the function raises the correct exceptions and that the `NotificationService` is not called."*

Copilot will generate a test case such as the following.

```python
    def test_deposit_negative_amount_raises_error(self):
        account = BankAccount(initial_balance=100, notification_system=self.notification_system)
        with self.assertRaises(ValueError):
            account.deposit(0)
        self.notification_system.notify.assert_not_called()
```

### Asking about areas of improvement

Now that we've written test cases to validate the integration functionality for deposits, this is a great opportunity to search for improvements within the test suite. While the current tests are functional, we can prompt Copilot to evalulate code coverage and suggest areas of improvement.

**Prompt:** *"What additional tests should be included to ensure full coverage for the integration between the `BankAccount` class and the `NotificationSystem`?"*

Prompting Copilot with this question can help you identify missing test cases that may have been overlooked.
In this situation, while we tested valid and invalid deposits, we haven't yet covered the withdrawal functionality.

Copilot will generate an updated test suite such as the following.



  <summary>Click to expand the full generated code example</summary>

```python
import unittest
from unittest.mock import Mock
from bank_account import BankAccount

class TestBankAccountIntegration(unittest.TestCase):
    def setUp(self):
        self.notification_system = Mock()

    def test_deposit_with_notification(self):
        account = BankAccount(initial_balance=100, notification_system=self.notification_system)
        account.deposit(50)
        self.assertEqual(account.get_balance(), 150)
        self.notification_system.notify.assert_called_once_with("Deposited 50, new balance: 150")

    def test_deposit_negative_amount_raises_error(self):
        account = BankAccount(initial_balance=100, notification_system=self.notification_system)
        with self.assertRaises(ValueError):
            account.deposit(-50)
        self.notification_system.notify.assert_not_called()

    def test_deposit_zero_amount_raises_error(self):
        account = BankAccount(initial_balance=100, notification_system=self.notification_system)
        with self.assertRaises(ValueError):
            account.deposit(0)
        self.notification_system.notify.assert_not_called()

    def test_withdraw_with_notification(self):
        account = BankAccount(initial_balance=100, notification_system=self.notification_system)
        account.withdraw(30)
        self.assertEqual(account.get_balance(), 70)
        self.notification_system.notify.assert_called_once_with("Withdrew 30, new balance: 70")

    def test_withdraw_exceeding_balance_raises_error(self):
        account = BankAccount(initial_balance=100, notification_system=self.notification_system)
        with self.assertRaises(ValueError):
            account.withdraw(150)
        self.notification_system.notify.assert_not_called()

    def test_withdraw_negative_amount_raises_error(self):
        account = BankAccount(initial_balance=100, notification_system=self.notification_system)
        with self.assertRaises(ValueError):
            account.withdraw(-30)
        self.notification_system.notify.assert_not_called()

    def test_withdraw_zero_amount_raises_error(self):
        account = BankAccount(initial_balance=100, notification_system=self.notification_system)
        with self.assertRaises(ValueError):
            account.withdraw(0)
        self.notification_system.notify.assert_not_called()

    def test_initial_negative_balance_raises_error(self):
        with self.assertRaises(ValueError):
            BankAccount(initial_balance=-100, notification_system=self.notification_system)

if __name__ == '__main__':
    unittest.main()
```




Once Copilot has generated the test suite to your satisfaction, run the tests with command below to verify the results.

```bash
python -m unittest test_bank_account_integration.py
```

## Using Copilot Spaces to improve test suggestions

Copilot Spaces is a feature that allows you to organize and share task-specific context with Copilot. This can help improve the relevance of the suggestions you receive. By providing Copilot with more context about your project, you can get better test suggestions.

For example, you could create a space that includes:

* The module you’re testing (like `payments.js`)
* The current test suite (like `payments.test.js`)
* A test coverage report or notes about what's missing

In the space, you can ask Copilot questions like:

> What test cases are missing in `payments.test.js` based on the logic in `payments.js`?

Or:

> Write a unit test for the refund logic in `refund.js`, following the structure in the existing test suite.

For more information about using Copilot Spaces, see [About GitHub Copilot Spaces](<https://docs.github.com/en/copilot/concepts/context/spaces>).


</details>
