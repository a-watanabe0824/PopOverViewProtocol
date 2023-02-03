# PopOverViewProtocol

```
import Foundation
import UIKit

//popoverを出すためのプロトコル。位置や矢印の方向は実装画面ごと、実装位置ごとに違うためpopを表示するViewController側で指定する。


protocol  PopoverViewdelegate where Self: UIViewController {
    
    //Popoverを呼び出す関数
    func Show_Popoverview(ViewVC:UIViewController, animated: Bool, completion: (() -> ())?)
    
    //表示位置を指定する関数
    func framePoint() -> CGRect
    
    //定義したのはいいが、いらないかも。
    func framePoint(_ SelectViewframe:CGRect) -> CGRect
    
    //矢印位置を指定する関数
    func arrowDirection() -> UIPopoverArrowDirection
    
    //矢印をleftにした時、viewの位置を調整する関数。
    //調整しない時はこのプロトコルの関数ごと消す。
    func leftframePoint(_ SelectViewframe:CGRect) -> CGRect
    
    //矢印をrightにした時、viewの位置を調整する関数。使う時はしたのextensionを削除して実装元で調整する。
    //調整しない時はこのプロトコルの関数ごと消す。
    func rightframePoint(_ SelectViewframe:CGRect) -> CGRect
    
    func viewframePoint(_ point:Int)
}

extension PopoverViewdelegate{
    //調整しない時はこのextensionとプロトコルの関数ごと消す。
    func rightframePoint(_ SelectViewframe:CGRect) -> CGRect{
        
        return SelectViewframe
    }
    //調整しない時はこのextensionとプロトコルの関数ごと消す。
    func leftframePoint(_ SelectViewframe:CGRect) -> CGRect{
        
        return SelectViewframe
    }
    
    func framePoint(_ SelectViewframe:CGRect) -> CGRect{
        
        return SelectViewframe
    }
    
    func viewframePoint(_ point:Int) {}
}

//where Self: UIViewControllerで限定することで遷移の処理を書くことができる。
extension PopoverViewdelegate{
    
    func Show_Popoverview(ViewVC:UIViewController, animated: Bool, completion: (() -> ())?){
    
        let SelectViewController = ViewVC
        
        //popoverで表示するViewController
        let PopoverView = SelectViewController
        
        //Popoverスタイル(モーダルで表示する)
        PopoverView.modalPresentationStyle = .popover
        
        //Popoverサイズ。ここでは呼び出し先のviewサイズをそのまま使う場合はPopoverView.view.frame.size。CGsize
        PopoverView.preferredContentSize = PopoverView.view.frame.size
        
        //ここからはviewController.popoverPresentationControllerを
        let POVPreCon = PopoverView.popoverPresentationController
        
        //arrowDirection()で矢印の表示方向を決める。
        //popを実装するUIViewControllerが表示先になるので、
        //プロトコルを実装した際に強制的にこのメゾットをやらせる。
        let Directions:UIPopoverArrowDirection = arrowDirection()
        
        //矢印の方向　permittedArrowDirections
        POVPreCon?.permittedArrowDirections = Directions
    
        //Popoverの中身を指定する。ここでは実際にpopで表示するUIViewController内のviewを指定している。
        POVPreCon?.sourceView = view
      
        //SelectViewframeでpopにだすviewのフレームを取得
        //表示位置などで計算に使う場合はこのフレーム値を使って表示位置を計算する。
        //right,leftでは、中心に表示されない。矢印が出る位置がsourceRectになるため呼び出しもとで中心を指定しても
        //呼び出し先のviewの半分の大きさでズレる。そのために、leftframePoint(）などを使い呼び出し元で位置を変更させている。
        
        let SelectViewframe = SelectViewController.view.frame
        
        switch Directions {
        
        case .up:
            POVPreCon?.sourceRect = framePoint()
            
        case .right:
            POVPreCon?.sourceRect = framePoint(SelectViewframe)
            
        case .left:
            POVPreCon?.sourceRect = leftframePoint(SelectViewframe)
            
        default:
            POVPreCon?.sourceRect = framePoint()
        }
        //遷移
        present(PopoverView, animated :animated ,completion : completion)
    }
}

```
