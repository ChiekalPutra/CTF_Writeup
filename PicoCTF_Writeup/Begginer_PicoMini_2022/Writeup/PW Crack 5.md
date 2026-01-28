# PW Crack 5

## Cara Menyelesaikan Tantangan Ini

- Unduh program, flag yang terenkripsi, hash, dan kamus, lalu letakkan semuanya di direktori yang sama.

- Buka program, dan ubah kodenya seperti ini.

- ```python
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

- Simpan dan jalankan programnya, flag akan tercetak.
