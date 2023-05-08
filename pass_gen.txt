import secrets
import string
import sys
from PyQt5.QtCore import Qt
from PyQt5.QtWidgets import QApplication, QWidget, QVBoxLayout, QHBoxLayout, \
    QLabel, QLineEdit, QPushButton, QMessageBox


class PassGen(QWidget):
    def __init__(self):
        super().__init__()

        self.label = QLabel("Password length:")
        self.length_input = QLineEdit()
        self.button = QPushButton("Generate password")
        self.pass_label = QLabel("")
        self.copy_button = QPushButton("Copy to clipboard")
        self.copy_box = QLineEdit()

        self.length_input.setAlignment(Qt.AlignRight)
        self.length_input.setFixedWidth(50)
        self.button.clicked.connect(self.generator)
        self.length_input.returnPressed.connect(self.generator)
        self.copy_button.clicked.connect(self.copy_password)
        self.copy_box.setReadOnly(True)
        self.pass_label.setAlignment(Qt.AlignCenter)

        length_layout = QHBoxLayout()
        length_layout.addWidget(self.label)
        length_layout.addWidget(self.length_input)

        password_layout = QVBoxLayout()
        password_layout.addWidget(self.pass_label)
        password_layout.addWidget(self.copy_button)

        main_layout = QVBoxLayout()
        main_layout.addLayout(length_layout)
        main_layout.addWidget(self.button)
        main_layout.addLayout(password_layout)
        self.setLayout(main_layout)

    def generator(self):
        try:
            pass_length = int(self.length_input.text())
        except ValueError:
            self.pass_label.setText("Invalid! Please enter a number!")
            return

        if pass_length < 8:
            self.pass_label.setText("The password length must be at least 8 "
                                    "characters long, for security reasons!")
            return

        characters = string.ascii_letters + string.digits + string.punctuation
        password = [secrets.choice(string.ascii_uppercase),
                    secrets.choice(string.ascii_lowercase),
                    secrets.choice(string.digits)]

        for i in range(pass_length - 3):
            password.append(secrets.choice(characters))
        secrets.SystemRandom().shuffle(password)
        password = ''.join(password)
        self.pass_label.setText(password)

    def copy_password(self):
        clipboard = QApplication.clipboard()
        clipboard.setText(self.pass_label.text())
        QMessageBox.information(self, "Password copied",
                                "The password has been "
                                "copied to the clipboard.")


if __name__ == "__main__":
    app = QApplication(sys.argv)
    window = PassGen()
    window.show()
    sys.exit(app.exec_())

