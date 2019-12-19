# Module-2-SVM-HOG-Object-Detection

屬於傳統的物件辨識器 : OpenCV與dlib的組合運用

採用技術 : 

  -判定影像演算法
  
    -HOG : 影像梯度方向直方圖

    -SVM : 支持向量機

  -搜尋物件方法
    
    -影像金字塔
    
    -非最大抑制
    
    -Hard Mining


1.用dlib已經打包好的模組

  #找尋圖片標籤
  -imutils.paths.list_images : 整合資料夾內檔案清單
  
    -str.rfind("") : 查找指定字串符號索引位置
    
    -str.split("") : 分割字串
    
    -str.replace("","") : 取代字串
  
  #擷取圖片標記方框
  
  -scipy.io.loadmat : 專用於讀取matlab file (因使用標記的軟件dlib imglab tool)
  
  -skimage.io.imread : 讀取影像 
    
  
  #訓練模型

  -dlib.train_simple_object_detector
    
    #檢視
    
    win = dlib.image_window()
    
    win.set_image(detector)
    
    dlib.hit_enter_to_continue()




2.逐步建立自己的物件辨識器

  1).pyramid影像縮小工具模組
    
    -for loop with yield return

  
  2).sliding_windows滑動視窗工具模組
  
    -雙迴圈視窗掃描圖像
    
    -通常會與影像縮小工具模組搭配應用
  

























