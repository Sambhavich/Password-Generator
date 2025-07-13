Introduction

Since cyber threats continue to increase, it is important to ensure that the use of strong, safe passwords for online accounts. A good password should be difficult to estimate, include a mixture of characters, and should be long to withstand the brut-form attacks. This password generator project provides a device to generate random, adaptable password based on the user-defined criteria. The program allows the user to include the length of the password and the types of characters (uppercase, lowercase, digits and special characters). It also evaluates the strength of the password and gives an indication to help the user remember it.



Features used in this Password Generator Code
Password Strength Check: The code checks the password strength considering the length and character type diversity (uppercase, lowercase, digits, special characters).
Password Creation: It creates a random password with user-specific choices for the length and types of characters.
Password Hint: After creating the password, the code gives a hint regarding the password length and character types included.
User Customization: The users can personalize their passwords by choosing the preferred length and types of characters (uppercase letters, lowercase letters, digits, and special characters).
Multi-User Support: The software can create individual passwords for a number of users.


Codes with explantation

1.Imports and Data strorage 

import random
import string
import hashlib
import time
from datetime import datetime

user_passwords = {}
user_hints = {}

Explanation
- We import necessary modules for random password generation, hashing, and time-based functions.
- Two dictionaries are used to simulate a simple in-memory database:
  - `user_passwords`: stores hashed passwords.
  - `user_hints`: stores password hints.
 Note: This is **not secure** for real applications.

2.Password Strength Maker
def password_strength(password):
    length = len(password)
    categories = sum([
        any(c.islower() for c in password),
        any(c.isupper() for c in password),
        any(c.isdigit() for c in password),
        any(c in string.punctuation for c in password)
    ])

    if length >= 12 and categories == 4:
        return "Strong"
    elif length >= 8 and categories >= 3:
        return "Medium"
    else:
        return "Weak"

 Explanation
This function evaluates password strength based on:
- **Length** of the password.
- Inclusion of 4 character types: lowercase, uppercase, digits, and symbols.
Returns one of three labels:
- `"Strong"`: 12+ characters with all 4 categories.
- `"Medium"`: 8+ characters with 3+ categories.
- `"Weak"`: Anything less.


3.Customizable Password Generator

def customizable_password(length=12):
    print("\n🔧 Customize your password:")
    use_upper = input("Include uppercase letters? (y/n): ").strip().lower() == 'y'
    use_lower = input("Include lowercase letters? (y/n): ").strip().lower() == 'y'
    use_digits = input("Include digits? (y/n): ").strip().lower() == 'y'
    use_symbols = input("Include special characters? (y/n): ").strip().lower() == 'y'

    char_set = ''

    if use_upper:
        char_set += string.ascii_uppercase
    if use_lower:
        char_set += string.ascii_lowercase
    if use_digits:
        char_set += string.digits
    if use_symbols:
        char_set += string.punctuation

    if not char_set:
        print("⚠ No character set selected. Defaulting to lowercase letters.")
        char_set = string.ascii_lowercase

    password = ''.join(random.choice(char_set) for _ in range(length))
return password


Explanation
Allows users to pick character types for their password:
- Uppercase, lowercase, digits, and/or symbols.
- If no options are selected, defaults to lowercase.
- Generates a password of given length (default: 12).

4.Unique Password Generator

def generate_unique_password(username, length=12, strength_mode='strong'):
    seed = hashlib.sha256((username + str(time.time())).encode()).hexdigest()
    random.seed(seed)

    if strength_mode == 'memorable':
        words = ['apple', 'orange', 'banana', 'grape', 'kiwi', 'cherry', 'peach', 'melon']
        password = '-'.join(random.choice(words) for _ in range(3))
    else:
        password = customizable_password(length)

return password

Unique Password Generator per User
- Combines username + current time to generate a unique seed.
- **Two modes**:
  - `'strong'`: Uses `customizable_password()`.
  - `'memorable'`: Uses simple fruit words (e.g., `apple-banana-grape`).

5.Strength vs Memorability option


def strength_vs_memorability(username):
    print("\n🔧 Choose password type:")
    print("1. Strong (More secure, harder to remember)")
    print("2. Memorable (Easier to remember, less secure)")

    choice = input("Enter 1 or 2: ")

    if choice == '1':
        password = generate_unique_password(username, length=16, strength_mode='strong')
    elif choice == '2':
        password = generate_unique_password(username, strength_mode='memorable')
    else:
        print("❌ Invalid choice! Defaulting to strong password.")
        password = generate_unique_password(username, length=16, strength_mode='strong')

    return password, choice

Explanation
Prompts user to choose:
1. **Strong Password** (16 characters, customizable).
2. **Memorable Password** (3 random fruit names).
Default = strong, if invalid input.

6: Provide Hint for Password

def provide_hint(username, password_type, password):
    print("\n💡 Let's create a hint to help you remember your password.")

    if password_type == '2':
        words_in_password = password.split('-')
        suggested_hint = f"It's about fruits: {', '.join(words_in_password)}"
    else:
        date_created = datetime.now().strftime("%B %d, %Y")
        suggested_hint = f"Strong password created on {date_created}. 16 chars with symbols."

    print(f"👉 Suggested hint: {suggested_hint}")

    custom_hint = input("Would you like to edit the hint? (y/n): ").strip().lower()
    if custom_hint == 'y':
        user_hint = input("Enter your custom hint: ").strip()
        if user_hint:
            user_hints[username] = user_hint
        else:
            print("⚠ Empty input! Using the suggested hint instead.")
            user_hints[username] = suggested_hint
    else:
        print("✅ Using suggested hint.")
        user_hints[username] = suggested_hint

Explanation
- Auto-generates a hint based on password type.
  - **Memorable**: Lists the fruit words.
  - **Strong**: Shows creation date and mentions symbols.
- Users can **edit** or **accept** the suggested hint.
- Hint is stored for retrieval later.

7: Retrieve Password Hint

def retrieve_hint(username):
    hint = user_hints.get(username)
    if hint:
        print(f"💡 Your hint is: {hint}")
    else:
        print("❌ No hint found for this user.")

Explanation

Fetches and displays the stored hint for a given username.
If no hint exists, informs the user.

.8: Main Program Logic

def main():
    print("=== 🔐 Welcome to the Custom Password Generator 🔐 ===")
    username = input("Enter your username: ").strip()

    password, pwd_type = strength_vs_memorability(username)

    print("\n✅ Your password has been generated!")
    print(f"🔑 Password: {password}")

    strength = password_strength(password)
    print(f"💪 Password Strength: {strength}")

    provide_hint(username, pwd_type, password)

    user_passwords[username] = hashlib.sha256(password.encode()).hexdigest()
    print("\n✅ Password securely stored (hashed). Don't forget it!")

    while True:
        print("\n--- MENU ---")
\        print("1. Retrieve password”)
 print("2. Generate new password")
        print("3. Exit")

        option = input("Select an option: ")

        if option == '1':
            retrieve_hint(username)
        elif option == '2':
            password, pwd_type = strength_vs_memorability(username)
            print(f"\n🔑 New Password: {password}")
            strength = password_strength(password)
            print(f"💪 Password Strength: {strength}")
            provide_hint(username, pwd_type, password)
            user_passwords[username] = hashlib.sha256(password.encode()).hexdigest()
        elif option == '3':
            print("👋 Exiting. Stay safe!")
            break
        else:
            print("❌ Invalid option. Try again.")

Explanation
- Gets username and generates initial password.
- Shows strength and stores hint.
- Allows repeated actions:
  1. Retrieve hint.
  2. Generate new password.
  3. Exit program.

9: Run Program

if __name__ == "__main__":
    main()

Explanation’
Starts the program by calling `main()` when this script is executed.

Advantages of Using this Password Generator Code
1.Customizable Passwords:
User Control: The program enables users to control their passwords by choosing the type of characters and password length they desire. Users are able to create passwords that suit their security requirements based on their own choice.
Flexible Options: The software is flexible in the sense that users can select from a range of characters (uppercase, lowercase, numbers, and special characters), which can help improve password strength markedly.

2.Password Strength Evaluation

Immediate Feedback: The strength analysis feature analyzes the created password and classifies it as weak, moderate, strong, or very strong. Users are provided with immediate feedback so that they are aware of how good their password is.
Security Guidance: The feedback on the strength of a password motivates users to enter stronger, safer passwords by keeping a combination of character types in mind and meeting the required length.

3.Password Hint:

User-Friendly: The password hint gives a summary of the password without disclosing the password itself. It specifies the password length and character types used, allowing users to remember the password pattern if they forget it.
Simplicity: The hint facility is straightforward and obvious, improving user experience and facilitating password recall.

4.Random Password Generation:

Improved Security: The passwords are random, hence less likely to be weak and predictable passwords most often targeted by hackers.
Does Not Use Common Passwords: Since the passwords are random, the program precludes the utilization of common passwords (such as "123456" or "password"), which are very unsafe.

5.Multi-User Support:

Multiple User Support: The software allows for the creation of passwords for multiple users, and thus it is a useful tool for organizations, teams, or families requiring secure password management.

Disadvantages of Using this Password Generator Code
1.Reliance on User Input:

Weak Passwords Based on Input: Although the code is customizable, the password strength largely relies on user input. When a user chooses a short password with few character types (such as only lower case letters), the password might be weak and subject to attack. The software does not promote best practices in strong password construction.
Potential for Inconsistent Security: Since the program depends on users to enter their choices, there is a chance that a user can create a weak password without realizing it if they are not aware of strong password guidelines.

2.Limited Complexity Checks:

No Password Complexity Enforcement: The software does not implement sophisticated password complexity requirements such as prohibiting easily guessable words, patterns (e.g., "qwerty"), or popular password combinations. Without complexity enforcement, the passwords may end up being weak even when technically created through a combination of character types.
No Blacklist of Common Passwords: The generator does not inhibit the use of common or compromised passwords. It enables the creation of weak, common passwords if the user chooses inappropriate parameters (e.g., short password length or common characters).

3.No Built-in Password Storage or Encryption

Password Storage Problem: The application does not securely store the generated password nor provide any encryption. In case the password is forgotten post-generation, it cannot be recovered through the application. The password has to be saved manually or noted down, which could be unsafe.
Security Risks with Storage: There is no provision to store or handle multiple passwords securely. This might be a constraint for users who are required to save passwords for various accounts.

4.Lack of User Education on Password Security

Limited Security Advice: The application lacks explicit advice on how to select robust passwords. Although it provides strength rating, it doesn't inform the user how to strengthen a poor password or mark security best practices.
No Suggestion to Use Different Passwords: The program does not prompt users to create different passwords for various services. Sharing passwords on multiple sites is a general security risk, and the program does not teach users about it.

5.Predictable Patterns in User-Generated Passwords

Repetitive User Selections: Although the system randomly generates the passwords, whenever users continuously pick the same-like preferences (like always opting for the same character set and size), the automatically generated passwords are likely to display repeating patterns or structures. That might be an opportunity for attacks from people well-versed in the user's habit. No Password Strength Degree Based on Situation

No Context-Aware Assessment: The password strength assessment is done purely on the basis of length and character variation, without context for the purpose of the service the password will be used for. For instance, a 12-character all-lowercase password may be rated as "strong" within the program but would remain insecure for high-security uses (such as banking or email).

Possible Security Risks

1.Brute Force Attacks:
Simple or short passwords created by the user are still susceptible to brute force attacks if the password length is minimal or if it has only lowercase letters and numbers.
The application does not require a minimum password length or complexity, which may leave passwords exposed to brute-force or dictionary-based attacks.

2.Password Guessing:
Unless they are very careful when selecting the password requirements, users might obtain weak passwords vulnerable to common forms of attack, such as dictionary attacks or frequent wordlist-based guessing.

3.Social Engineering and Clues:
The password reminder, while helpful, may also be a threat if the reminder is too explicit. If the attackers are familiar with the user or have knowledge of general facts about the user, they may try to use the reminder to guess the password with increased ease.

4.Password Reuse:
The application fails to highlight the importance of having distinct passwords for every service. Users can end up having the same password for multiple sites, which poses a greater risk of data breaches across numerous accounts.

Conclusion
The Password Generator script is an effective and simple tool for generating random and safe passwords. Its primary advantages include flexibility, customization of passwords, and password strength evaluation. Yet, it does come with user input dependency, no complexity check, and a few security issues such as possible weak passwords and password reuse.

While such code can serve as a decent foundation for producing random passwords, other security functionality, including ensuring strong password procedures, password storage protocols, and educating users in password security, would make it much more effective and secure.





