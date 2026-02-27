# Cache Schema

## 1) 存储位置

默认缓存目录：`/home/claude/maimai_cache/`

文件：

- `job_configs.json`
- `outreach_history.json`

如果默认目录不可写，改用当前可写目录，并在执行报告中注明实际路径。

## 2) job_configs.json

用途：保存岗位硬性要求、公司分组、搜索轮次、评分参数、触达上下文。

示例：

```json
{
  "last_updated": "2026-02-26T10:00:00",
  "jobs": {
    "AI算法工程师": {
      "hard_requirements": {
        "school": "985",
        "major": "计算机相关",
        "experience_years": "1-5",
        "company_background": "必须有大厂/AI独角兽/研究院经历"
      },
      "company_lists": {
        "互联网大厂": ["字节跳动", "阿里巴巴", "腾讯"],
        "AI独角兽": ["智谱", "月之暗面", "MiniMax", "DeepSeek"],
        "AI研究院": ["上海人工智能实验室", "微软亚洲研究院"]
      },
      "search_rounds": [
        {
          "round_name": "一线互联网大厂",
          "keyword": "算法",
          "maimai_filters": {
            "city": "上海",
            "education": "本科及以上",
            "experience": "1-3年",
            "school": "985",
            "company": "一线互联网公司"
          }
        }
      ],
      "scoring": {
        "company_tier": {
          "顶级AI公司在职": 40,
          "大厂在职": 38,
          "顶级AI公司历史": 35,
          "大厂历史": 33,
          "其他优质科技": 20
        },
        "school_tier": {
          "C9": 30,
          "985强校": 25,
          "其他985": 22
        },
        "activity": {
          "今日活跃": 15,
          "近1周活跃": 12,
          "近1月活跃": 8,
          "无标签": 5
        },
        "intent": {
          "正在看机会": 15,
          "关注行情": 10,
          "半年不看机会": 5,
          "未知": 8
        }
      },
      "outreach_context": {
        "company_name": "黑湖智造",
        "team_desc": "AI团队",
        "opportunity_highlights": ["工业智能场景", "算法落地", "技术驱动"],
        "tone": "平等对话，不卑不亢"
      }
    }
  }
}
```

## 3) outreach_history.json

用途：去重、统计、复盘。

示例：

```json
{
  "contacted": {
    "脉脉_张三_智谱_2026-02-26": {
      "name": "张三",
      "company": "智谱",
      "score": 85,
      "message_sent": "...",
      "round": "AI独角兽"
    }
  },
  "stats": {
    "2026-02-26": {
      "total_viewed": 45,
      "passed_filter": 18,
      "contacted": 12,
      "rounds_completed": ["一线互联网大厂", "AI独角兽"]
    }
  }
}
```

## 4) 读写规则

- 启动时先读缓存，再决定“确认/调整”。
- 每次发送成功后立即落盘，不等批量结束。
- 以 `姓名+公司+日期` 作为最低去重键；可额外加入平台 ID。
- 发生异常中断时，确保已成功发送记录不丢失。
