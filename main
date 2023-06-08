package main  
  
import (  
   "bytes"  
   "encoding/json"   "fmt"   "net/http"   "time"  
   "github.com/gin-gonic/gin")  
  
type Message struct {  
   MessageType string `json:"message_type"`  
   GroupId     int64  `json:"group_id"`  
   Message     string `json:"message"`  
}  
  
var msg Message  
  
type ImageResponse struct {  
   ImageUrl string `json:"imageUrl"`  
}  
  
var imageMessage string  
  
func main() {  
   router := gin.Default()  
   msg.GroupId = 319021060  
   router.POST("/", func(c *gin.Context) {  
  
      //群号  
  
      //将json格式的context转化成go结构体  
      if err := c.ShouldBindJSON(&msg); err != nil {  
         c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})  
         return  
      }  
      //测试回复：群友发送“hi”时，bot回复“hello”  
      if msg.Message == "hi" {  
         reply := Message{  
            MessageType: "group",  
            GroupId:     msg.GroupId,  
            Message:     "hello",  
         }  
  
         jsonBytes, err := json.Marshal(reply)  
         if err != nil {  
            c.JSON(http.StatusInternalServerError, gin.H{"error": err.Error()})  
            return  
         }  
  
         resp, err := http.Post("http://localhost:5700/send_msg", "application/json", bytes.NewBuffer(jsonBytes))  
         if err != nil {  
            c.JSON(http.StatusInternalServerError, gin.H{"error": err.Error()})  
            return  
         }  
         defer resp.Body.Close()  
  
         c.JSON(http.StatusOK, gin.H{"msg": "ok"})  
         return  
      }  
      //今日新闻api链接的处理  
      apiUrl := "http://api.03c3.cn/zb/api.php"  
      resp, err := http.Get(apiUrl)  
      if err != nil {  
         panic(err)  
      }  
      defer resp.Body.Close()  
      var imageResponse ImageResponse  
      json.NewDecoder(resp.Body).Decode(&imageResponse)  
      imageUrl := imageResponse.ImageUrl  
      imageMessage = fmt.Sprintf("[CQ:image,file=%s]", imageUrl)  
  
      //群友发送"今日新闻"，bot发送带有今日新闻的图片  
      if msg.Message == "今日新闻" {  
         reply := Message{  
            MessageType: "group",  
            GroupId:     msg.GroupId,  
            Message:     imageMessage,  
         }  
  
         jsonBytes, err := json.Marshal(reply)  
         if err != nil {  
            c.JSON(http.StatusInternalServerError, gin.H{"error": err.Error()})  
            return  
         }  
  
         resp, err := http.Post("http://localhost:5700/send_msg", "application/json", bytes.NewBuffer(jsonBytes))  
         if err != nil {  
            c.JSON(http.StatusInternalServerError, gin.H{"error": err.Error()})  
            return  
         }  
         defer resp.Body.Close()  
  
         c.JSON(http.StatusOK, gin.H{"msg": "ok"})  
         return  
      }  
      c.JSON(http.StatusOK, gin.H{"msg": "ok"})  
   })  
   go func() {  
      for {  
         now := time.Now()  
         if now.Hour() == 12 && now.Minute() == 56 && now.Second() == 0 {  
            msg := Message{  
               MessageType: "group",  
               GroupId:     msg.GroupId, // 这里需要替换成你的QQ群ID  
               Message:     "Hello! 今天的新闻如下" + imageMessage + "May the good day！",  
            }  
  
            jsonBytes, err := json.Marshal(msg)  
            if err != nil {  
               // 处理错误  
            }  
  
            _, err = http.Post("http://localhost:5700/send_msg", "application/json", bytes.NewBuffer(jsonBytes))  
            if err != nil {  
               // 处理错误  
            }  
         }  
  
         // 每隔一秒钟检查一次是否到达定时发送的时间  
         time.Sleep(time.Second)  
      }  
   }()  
  
   router.Run(":5701")  
}
