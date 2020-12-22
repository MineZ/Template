# Mission

任务系统，它终于来了！根据运营经验，任务系统是有效提高DAU的关键因素之一，提供丰富的任务种类可大幅增加游戏可玩性和玩家留存。MINEZ依据自身产品定性，设计了符合大众玩家口味、同时不失趣味的任务熊，目前已支持每日随机任务，多步骤任务，多种奖励，与NPC好感度、铭牌、通行证系统关联等强大功能。

以下是包含全部定制化功能的任务模板，请仔细阅读后再进行任务的创作哦！

```yaml
mission_template: # 代表任务ID, 必须唯一, 可自定义.
  display-name: '<&c>示例任务名称' # 必填, 代表任务名称, 颜色代码用Denizen标签表示.
  start-time: '<time[2020/12/1]>' # 可选, 代表任务开始时间, 超过此时间后任务才可由trigger中的设定项激活.
  expire-time: '<time[2020/12/7]>' # 可选, 代表任务结束时间, 以Denizen的TimeTag表示. 超过此时间后任务会立刻终止作废, steps中未完成项的奖励不可再获得.
  allow-repeat: false # 必填, 代表任务是否可重复.
  trigger: 'none' # 必填, 代表激活任务的方式, 任务将从steps的首项开始. 选填项: none(只能由特定事件激活), auto(持续保持激活状态), dialog(在对话框中选择指定项后获得), search(搜索一件物体后激活), break|place(破坏|放置方块后激活), join|leave(进入|离开某一Denizen Cuboid后激活)... 更多触发项正在持续追加中, 欢迎补充.
  steps: # 必填, 代表任务步骤.
    '1': # 必填, 代表任务序号, 必须为非0自然数, 并按照升序排列. 如希望任务完成后再给予玩家奖励, 只为steps中最后一项设置rewards即可.
      type: 'dialog' # 必填, 代表该步骤任务类型. 此处代表对话类型任务, 在对话框中选择指定项后即可获得奖励.
      dialog: 'kachesiji_hello' # 可选, 填写对话框ID.
      selection: # 可选, 必须为列表格式, 代表对话框中选择任一项后即可完成步骤, 并进入下一步.
      - '1'
      - '2'
      - '4'
      selection-others: 'pass' # 可选, 代表在该对话框中若选择其他项, 任务将如何进展. 选填项: fail(任务失败), pass(跳过该步骤并忽略奖励, 直接进入下一步骤), repeat(重置该步骤).
      rewards: # 可选, 代表完成该步骤后可获得的奖励
        points: '600' # 可选, 代表通行证点数奖励. 负数代表扣除.
        friendship: # 可选, 代表NPC好感度变化. 负数代表扣除.
          '0': # 必填, 代表NPC的ID. 使用/npc select选中NPC后可通过/npc id查询该NPC的ID.
            points: '1' # 必填, 代表该NPC对玩家好感度的变化.
          '1':
            points: '-1'
          '5':
            points: '3'
        silver: '1000' # 可选, 代表给予玩家的银卡奖励数量. 负数代表扣除.
        gold: '10' # 可选, 代表给予玩家的金卡奖励数量. 负数代表扣除.
        nameplates: # 可选, 代表给予玩家的称号奖励.
        - 'xinshou'
        items: # 可选, 代表给予玩家的物品奖励, 可设置Denizen物品属性.
        - 'weapon_shihunzhe'
        - 'diamond_block[quantity=1;display=<&b><&o><player.name>的专属钻石]'
        permissions: # 可选, 代表给予玩家的权限奖励.
          'minez.notification.banwarn':
            is-temp: true # 必填, 代表该权限是具有临时性.
            duration: '1mo3d13h45m' # 可选, 代表该权限的持续时间. 必须遵守LuckPerms写法. 详情参考: https://luckperms.net/wiki/Permission-Commands#lp-usergroup-usergroup-permission-settemp-node-truefalse-duration-temporary-modifier-context.
            modifier: 'accumulate' # 可选, 代表该权限的持续时间覆盖方式. 必须遵守LuckPerms写法. 详情参考上一行链接.
          'minez.command.minez':
            is-temp: false
    '2':
      type: 'search' # 搜索类型任务.
      objects: # 计入任务步骤有效次数的物体. 一律采用蛇形命名法. 必须使用Spigot Material常量名. 详情参考: https://hub.spigotmc.org/javadocs/spigot/org/bukkit/Material.html.
      - 'grass'
      - 'tall_grass'
      - 'fern'
      - 'large_fern'
      search-times: '20' # 完成该步骤所需搜索次数.
    '3':
      type: 'kill' # 击杀类型任务.
      entities: # 计入任务步骤有效次数的实体类型. 一律采用蛇形命名法. 必须使用Spigot EntityType常量名. 详情参考: https://hub.spigotmc.org/javadocs/bukkit/org/bukkit/entity/EntityType.html.
      - 'sheep'
      - 'pig'
      - 'cow'
      kill-times: '15' # 完成该步骤所需击杀次数.
```

此外，玩家的任务相关数据一律存储于/data/missions_data.yml文件中。一份玩家数据模板如下：
```yaml
649be37d-7996-40e7-8b00-bf989ac85ebc: # 玩家UUID
  completed: # 已完成的任务
  - start
  - mission_1
  - mission_2
  - mission_3
  failed: # 已失败的任务
  - mission_4
  - mission_5
  in-progress: # 正在进行中的任务
    mission_dialog: # 示例对话类型任务步骤.
      step: '2' # 正在进行中的步骤.
    mission_search: # 示例搜索类型任务步骤.
      step: '1'
      search-times: '15'
    mission_kill: # 示例击杀类型任务步骤.
      step: '1'
      kill-times: '12'
```

嘿嘿，怎么样，是不是感觉很简单呢？让我们一起为玩家们带来精彩而充实的任务吧！
