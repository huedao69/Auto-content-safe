name: Chế độ an toàn nội dung tự động

on:
  workflow_dispatch:
  schedule:
    - cron: "0 */2 * * *"  # 2 giờ/lần (UTC)

jobs:
  chay:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.11"

      - name: Install requirements
        run: |
          python -m pip install --upgrade pip
          if [ -f requirements.txt ]; then pip install -r requirements.txt; fi

      - name: Run Safe Mode
        env:
          WORDPRESS_BASE_URL: ${{ secrets.WORDPRESS_BASE_URL }}
          WORDPRESS_USERNAME: ${{ secrets.WORDPRESS_USERNAME }}
          WORDPRESS_APP_PASSWORD: ${{ secrets.WORDPRESS_APP_PASSWORD }}
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
          SAFE_MODE: "true"                  # luôn đăng ở trạng thái Pending
          TZ: Asia/Ho_Chi_Minh
        run: |
          # thử chạy với --once, nếu script không hỗ trợ thì chạy không tham số
          python main.py --once || python main.py
