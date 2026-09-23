![alt text](image.png)

code
```
for i in $(seq 1 50); do
  HTML=$(curl -s http://172.198.228.9)
  if echo "$HTML" | grep -q "Yahdilil Haq Sarifuddin"; then
    echo "Yahdilil Haq Sarifuddin"
  elif echo "$HTML" | grep -q "Darwisy Ahmad Alfayyadl"; then
    echo "Darwisy Ahmad Alfayyadl"
  elif echo "$HTML" | grep -q "Muhammad Adinata Parikesit"; then
    echo "Muhammad Adinata Parikesit"
  elif echo "$HTML" | grep -q "Hilmy Fausta Pratama"; then
    echo "Hilmy Fausta Pratama"
  else
    echo "Unknown"
  fi
  sleep 0.3
done | tee load_test_results.txt
```