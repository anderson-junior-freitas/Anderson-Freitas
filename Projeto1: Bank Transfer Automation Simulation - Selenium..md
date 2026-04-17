// PROJETO: Bank Transfer Automation Simulation - Selenium (Double Confirmation)
// Cenário de negócio (o coração do projeto)

Transferência entre instituições com dupla confirmação:

// Fluxo
// 1. Transferidor inicia:
// Código agregador
// Valor
// Conta do adquirente

// 2. Adquirente confirma:
// Código agregador do transferidor
// Valor
// Conta do transferidor

// Regras de validação
// Caso MATCH
// Dados batem
// Resultado esperado:

"Operação realizada com sucesso"

// Saldo do adquirente atualizado ✔

// Caso NÃO MATCH
// Dados divergentes
// Resultado experado:

// "Pendente de confirmação"

// Após "00:00h"

// "Expirada"

// Estrutura do projeto

bank-transfer-automation/
│
├── tests/
│   ├── test_success_transfer.py
│   ├── test_pending_transfer.py
│
├── pages/
│   ├── login_page.py
│   ├── transfer_page.py
│   ├── confirmation_page.py
│
├── utils/
│   ├── driver_factory.py
│   ├── data_generator.py
│
├── requirements.txt
├── README.md

// Setup

pip install selenium pytest webdriver-manager

// Driver

📄 driver_factory.py

from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from webdriver_manager.chrome import ChromeDriverManager

def get_driver():
    options = webdriver.ChromeOptions()
    options.add_argument("--start-maximized")
    
    driver = webdriver.Chrome(service=Service(ChromeDriverManager().install()), options=options)
    return driver

// Login

📄 login_page.py

from selenium.webdriver.common.by import By

class LoginPage:

    def __init__(self, driver):
        self.driver = driver

    def login(self, user, password):
        self.driver.get("https://example-bank.com/login")
        self.driver.find_element(By.ID, "username").send_keys(user)
        self.driver.find_element(By.ID, "password").send_keys(password)
        self.driver.find_element(By.ID, "login-btn").click()

// Transferência (Transferidor)

📄 transfer_page.py

from selenium.webdriver.common.by import By

class TransferPage:

    def __init__(self, driver):
        self.driver = driver

    def start_transfer(self, aggregator_code, amount, receiver_account):
        self.driver.find_element(By.ID, "aggregator").send_keys(aggregator_code)
        self.driver.find_element(By.ID, "amount").send_keys(amount)
        self.driver.find_element(By.ID, "receiver").send_keys(receiver_account)
        self.driver.find_element(By.ID, "submit-transfer").click()

// Confirmação (Adquirente)

📄 confirmation_page.py

from selenium.webdriver.common.by import By

class ConfirmationPage:

    def __init__(self, driver):
        self.driver = driver

    def confirm_transfer(self, aggregator_code, amount, sender_account):
        self.driver.find_element(By.ID, "aggregator-confirm").send_keys(aggregator_code)
        self.driver.find_element(By.ID, "amount-confirm").send_keys(amount)
        self.driver.find_element(By.ID, "sender").send_keys(sender_account)
        self.driver.find_element(By.ID, "confirm-btn").click()

    def get_status_message(self):
        return self.driver.find_element(By.ID, "status").text

// TESTE 1 — Transferência com sucesso (MATCH)

📄 test_success_transfer.py

from utils.driver_factory import get_driver
from pages.login_page import LoginPage
from pages.transfer_page import TransferPage
from pages.confirmation_page import ConfirmationPage

def test_success_transfer():

    driver = get_driver()

    login = LoginPage(driver)
    transfer = TransferPage(driver)
    confirm = ConfirmationPage(driver)

    # Login transferidor
    login.login("userA", "123")

    transfer.start_transfer("ABC123", "1000", "ACC456")

    # Login adquirente
    login.login("userB", "123")

    confirm.confirm_transfer("ABC123", "1000", "ACC123")

    message = confirm.get_status_message()

    assert "sucesso" in message.lower()

    driver.quit()

// TESTE 2 — Transferência pendente (NO MATCH)

📄 test_pending_transfer.py

from utils.driver_factory import get_driver
from pages.login_page import LoginPage
from pages.transfer_page import TransferPage
from pages.confirmation_page import ConfirmationPage

def test_pending_transfer():

    driver = get_driver()

    login = LoginPage(driver)
    transfer = TransferPage(driver)
    confirm = ConfirmationPage(driver)

    login.login("userA", "123")

    transfer.start_transfer("ABC123", "1000", "ACC456")

    login.login("userB", "123")

    # valor diferente → não bate
    confirm.confirm_transfer("ABC123", "999", "ACC123")

    message = confirm.get_status_message()

    assert "pendente" in message.lower()

    driver.quit()

    // (PLUS) Simulação de expiração

import time

time.sleep(5)  # simulação
assert "expirada" in message.lower()

// Resumo da operação de Transferência

// Bank Transfer Automation - Selenium

// Overview
// Projeto de automação de testes simulando transferências bancárias com dupla confirmação.

// Fluxo testado
// - Transferidor inicia operação
// - Adquirente confirma dados
// - Validação de match entre informações

//Cenários
// - Transferência com sucesso (match)
// - Transferência pendente (dados divergentes)
// - Expiração da operação

// Tecnologias
// - Python
// - Selenium WebDriver
// - Pytest
// - Page Object Model (POM)

// Objetivo
// Validar fluxos críticos financeiros, garantindo integridade de dados e segurança da operação.

// Autor
// Anderson Freitas
