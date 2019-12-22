# Module-4-SVM-HOG-Object-Detection

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

  建立的模組工具:

  1).pyramid影像縮小工具模組
    
    -for loop with yield return

  
  2).sliding_windows滑動視窗工具模組
  
    -雙迴圈視窗掃描圖像
    
    -通常會與影像縮小工具模組搭配應用
  
  
  3).訓練參數配置的建立json(我們需要建立這個訓練器的Configure)
    
    -python open io with read file as string
    
    -json.loads : 讀取json文件變為dict
    
    -使用json_minify : 可有效將類似json格式的字串(非json)優化為json.loads模組可讀之文件
    
    -善用class建立物件擁有字典功能
      
      -self.__dict__.update()
      
      -def __getitem__(self, k):
        
        return self.__dict__.get(k, None)
        
    -善用golb.glob針對特有檔案格式整合資料夾內檔案清單(ex .mat)
      
      -以便用於統籌所有標記方框之大小均值等等 : 以便用於後續HOG計算的參數配置
        
    -文件檔擺放內容
    
      -初始路徑位置 : 正向訓練資料 ; 正向訓練資料註解 ; 反向訓練資料
      
      -特徵擷取 : 特徵資料路徑(hdf5) ; percent_gt_images ; offset ; use_flip ; num_distraction_images ; num_distractions_per_image
      
      -HOG參數 : 方向數 ; pixels_per_cell ; cells_per_block ; 資料標準化bool
      
      -物件偵測 : 滑動視窗位移步伐 ; overlap_thresh ; 圖像縮放比例 ; 滑動視窗大小 ; 信心程度閾值
      
      -SVM學習器參數 : 學習器模型存放路徑 ; C懲罰係數
      
      -HARD NEGATIVE MINING : hn_num_distraction_images , hn_window_step , hn_pyramid_scale , hn_min_probability
     
    
  4).HOG全局影像描述器的建立
  
  
  5).建立儲存器工具
  
    -H5PY : Python 模組小型資料庫
    
    -儲存資料
    
      #建立資料庫路徑與讀寫IO
      db = h5py.File(path, "w")
    
      #創建資料庫 : 資料庫名稱 ; (資料比數長度 , 資料column長度+1(擺放標籤用)) ; 儲存資料類型
      dataset = db.create_dataset(datasetName, (len(data), len(data[0]) + 1), dtype="float")
      
      #擺入資料
      dataset[0:len(data)] = np.c_[labels, data]
      
      #卸載儲存器
      db.close()
    
    -讀取資料
      
      #連結資料庫
      db = h5py.File(path, "r")
      
      #讀取資料
      (labels, data) = (db[datasetName][:, 0], db[datasetName][:, 1:])
      
      #卸載儲存器
      db.close()

  6).物件辨識工具
  
    -整合pyramid影像縮小工具與sliding_windows滑動視窗工具
    
    -採取模型判定機率是否超過設定閾值而篩出
  
  7).非最大抑制
  
    -幾個不同框選範圍但重複框選區域集中的框，非最大抑制就是在這些框中先篩選辨識機率最大的那一個框予以保留，其餘則淘汰
    
  8).錯誤資料的重複訓練
  
    -訓練資料集會包含實際有需要辨識的物件(正)的圖片資料以及用以作為無物件的圖片(反)
    
    -在第一次訓練模型的時候我們使用的是
      
      -原始正向數據集
      
      -原始反向數據集
    
    -採用第一次訓練的模型，進行物件判別，當判別錯誤的資料將集蒐集
      
      -進行第二次的訓練模型 
        
        -原始正向數據集
      
        -原始反向數據集
      
        -Hard-negatives

  9).整合流程
    
    1.explore_dims.py : 計算訓練資料相關基本資訊，以方便後續參數建置
    
    2.extract_features.py : 透過HOG影像描述建立特徵並標記標記並儲存於Hdf5
    
    3.train_model : 首次訓練模型(SVM)
    
    4.test_model_no_nms : 無非最大抑制的測試辨識狀況
    
    5.test_model_with_nms : 加入非最大抑制的測試辨識狀況
    
    6.hard_negtive_mine : 將辨識錯誤的資料加入訓練集
    
    7.train_model : 再次訓練模型(SVM)
    
    8.test_model_with_nms : 加入非最大抑制的測試辨識狀況
    
















