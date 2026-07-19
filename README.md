# Really-
<p align="center">
  <!-- الفورم اللي بيرسل البيانات للـ C++ تلقائياً -->
  <form action="/calculate" method="GET" style="text-align: center; margin-top: 30px; font-family: sans-serif;">
    
    <label style="color: white; font-size: 18px;">Enter your birth details:</label><br><br>
    
    <!-- صناديق إدخال اليوم، الشهر، والسنة -->
    <input type="number" name="day" placeholder="Day (يوم)" min="1" max="31" required style="padding: 10px; width: 80px; border-radius: 5px; border: none; margin: 5px; text-align: center;">
    <input type="number" name="month" placeholder="Month (شهر)" min="1" max="12" required style="padding: 10px; width: 100px; border-radius: 5px; border: none; margin: 5px; text-align: center;">
    <input type="number" name="year" placeholder="Year (سنة)" min="1900" max="2026" required style="padding: 10px; width: 100px; border-radius: 5px; border: none; margin: 5px; text-align: center;">
    
    <br><br>
    <!-- زر الحساب الملون -->
    <button type="submit" style="background-color: #39d353; color: black; font-weight: bold; border: none; padding: 12px 30px; font-size: 16px; border-radius: 5px; cursor: pointer;">
      Calculate your Age
    </button>

  </form>
</p>
