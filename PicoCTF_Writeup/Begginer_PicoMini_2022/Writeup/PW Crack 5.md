# PW Crack 5

## How To Solve This Challenge

- Download the program, encrypted flag, hash, and the dictionary, place them in the same directory

- Open the program, and change the code like this
  
  ```python
  def level_5_pw_check(user_pw):
  
      user_pw_hash = hash_pw(user_pw)
      
      if( user_pw_hash == correct_pw_hash ):
          print("Welcome back... your flag, user:")
          decryption = str_xor(flag_enc.decode(), user_pw)
          print(decryption)
          return
     # print("That password is incorrect")
  
  
  with open('dictionary.txt') as dt:
      lines = dt.readlines()
      for line in lines:
          line = line.strip()
          level_5_pw_check(line)
  
  ```

- Save and run the program, the flag should be printed
