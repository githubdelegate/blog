## UndoRedo


1. 项目中经常会遇到undo redo 操作，要么自己实现，但是自己实现会有很对问题 ，系统提供了undo redo
2. 每个ViewController 都有一个默认的undoManager
3. raywenderlich 这片文章很好 undo教程​
1. 核心思想是， 修改数据，通过数据的改变来刷新界面， 就是响应式那种方式，不要直接修改界面或者逻辑，一切的落脚点都是数据
2. 具体代码看文件
3. 核心代码学习
    1. undo 核心函数就是这个，当点击undo 按钮的时候，就会执行注册过的block，这个block可以捕获值，就是下面的diff ，每次点击undo的时候，执行的block里面的diff数据都是以前注册的时候捕获的, diff 都是struct 
    2. A closure can capture constants and variables from the surrounding context in which it’s defined. The closure can then refer to and modify the values of those constants and variables from within its body, even if the original scope that defined the constants and variables no longer exists.

        ```swift
            self.undoManager?.registerUndo(withTarget: self) { target in
                    target.modifyModel{ model in
                        model = diff.from
                    }
                }
        ```


    3. DIFF code

```swift 

/// 绘制模型
struct ZYZDrawModel {
    var penpaths: [ZYZPenTool] = []
    struct Diff {
        let from: ZYZDrawModel
        let to:ZYZDrawModel
        let change: DrawChange
        
        enum DrawChange {
            case inserted(ZYZPenTool)
            case removed(ZYZPenTool)
            case none
        }
    }
    
    func diffed(with other: ZYZDrawModel) -> Diff {
        var change: Diff.DrawChange = .none
        if self.penpaths.count > other.penpaths.count {
            change = .removed(self.penpaths.last!)
        } else {
            change = .inserted(other.penpaths.last!)
        }
      return Diff(from: self, to: other, change: change)
    }
}

  
```

    4. 修改数据代码

```swift
 /// 修改数据源
    func modifyModel(_ mutations: (inout ZYZDrawModel) -> Void) {
        var model = self.drawModel
        let oldModel = model
        mutations(&model)
        let modelDiff = oldModel.diffed(with: model)
        drawModelDidChange(diff: modelDiff)
    }
    
    func drawModelDidChange(diff: ZYZDrawModel.Diff) {
        self.drawModel = diff.to
        self.drawLine()
        self.undoManager?.registerUndo(withTarget: self) { target in
            target.modifyModel{ model in
                model = diff.from
            }
        }
      
      DispatchQueue.main.async {
          self.refreshUndoRedo()
      }
    }
    
    private func refreshUndoRedo() {
        print(" undo = \(self.undoManager?.canUndo)- redo=\(String(describing: self.undoManager?.canRedo) ?? "")")
        self.redoBtn.isEnabled = self.undoManager?.canRedo ?? false
        self.redoBtn.isSelected = self.redoBtn.isEnabled
        self.undoBtn.isEnabled = self.undoManager?.canUndo ?? false
        self.undoBtn.isSelected = self.undoBtn.isEnabled
    }
```
> [学习代码](./People-Keeper.zip)

