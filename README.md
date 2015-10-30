# ColloctionView
自定义ColloctionViewlLayout

class SecondCollectionViewLayout: UICollectionViewLayout {
    
    var cacheDic:NSMutableDictionary = NSMutableDictionary(capacity: 0)
    
    override func prepareLayout() {
        super.prepareLayout()
        self.registerClass(SecondCollectionReusableView.self, forDecorationViewOfKind: "SecondCollectionReusableView")
    }
 
    override func collectionViewContentSize() -> CGSize {
        var  height:CGFloat = 0.0
       let value = cacheDic.objectForKey(NSNumber(integer: (collectionView?.numberOfSections())! - 1))
        if(value != nil)
        {
            height = (value?.CGRectValue.height)! + (value?.CGRectValue.origin.y)!
        }else
        {
            height = (collectionView?.frame.size.height)!
        }
        return CGSizeMake((collectionView?.frame.size.width)!, height)
    }
    
     // 所有单元格位置属性
    override func layoutAttributesForElementsInRect(rect: CGRect) -> [UICollectionViewLayoutAttributes]? {
        var attributeArray = [UICollectionViewLayoutAttributes]()
        let section = collectionView?.numberOfSections()
        
        for (var i = 0 ; i < section; i++)
        {
            let attributeDea = layoutAttributesForDecorationViewOfKind("SecondCollectionReusableView", atIndexPath: NSIndexPath(forItem: 0, inSection: i))
            attributeArray.append(attributeDea!)
            let cellS = collectionView?.numberOfItemsInSection(i)
            for (var j = 0 ; j < cellS ; j++) {
                 let indexPath = NSIndexPath(forItem: j, inSection: i)
                 let attribute = layoutAttributesForItemAtIndexPath(indexPath)
                attributeArray.append(attribute!)
            }
            
        }
        
        return attributeArray
    }
    
    override func layoutAttributesForDecorationViewOfKind(elementKind: String, atIndexPath indexPath: NSIndexPath) -> UICollectionViewLayoutAttributes? {
        let attributes = UICollectionViewLayoutAttributes(forDecorationViewOfKind: elementKind, withIndexPath: indexPath)
    let sectionValue = NSNumber(integer: indexPath.section)
       if(cacheDic.objectForKey(sectionValue) == nil)
       {
        let a = ((collectionView?.numberOfItemsInSection(indexPath.section))! - 1) % 3
        var row = 0
        if(a != 0)
        {
            row = ((collectionView?.numberOfItemsInSection(indexPath.section))! - 1 ) / 3 + 1
        }else
        {
            row = ((collectionView?.numberOfItemsInSection(indexPath.section))! - 1 ) / 3
        }
            attributes.size = CGSizeMake((collectionView?.frame.size.width)! - 20, CGFloat(160 + (row - 1) * (10 + 100 )))
        //let space = 20
        
        if(indexPath.section != 0)
        {
           //根据上一个frame来定位置
           let frame = cacheDic.objectForKey(NSNumber(integer: indexPath.section - 1))?.CGRectValue
            attributes.center =  CGPointMake((collectionView?.frame.size.width)! / 2, (frame?.origin.y)! + (frame?.size.height)! + 20  + attributes.size.height / 2 )
        }else
        {
           attributes.center = CGPointMake((collectionView?.frame.size.width)! / 2, CGFloat(((160 + (row - 1) * (10 + 100 )) / 2 ) + (160 + (row - 1) * (10 + 100 ) + 20) * indexPath.section ) )
        }
        
        let value = NSValue(CGRect: attributes.frame)
        cacheDic .setObject(value, forKey: sectionValue)
        }else
       {
          attributes.frame = (cacheDic.objectForKey(sectionValue)?.CGRectValue)!
        }
        attributes.zIndex = -1

        return attributes
    }
    
    
    
    override func layoutAttributesForItemAtIndexPath(indexPath: NSIndexPath) -> UICollectionViewLayoutAttributes? {
        let attributes = UICollectionViewLayoutAttributes(forCellWithIndexPath: indexPath)
        let aecorationViewFrame = cacheDic.objectForKey(NSNumber(integer: indexPath.section))?.CGRectValue
        var side = ((aecorationViewFrame?.size.width)! - 4 * 5) / 3
        var height = 100
        let orignY = 51 + 5
        var x = (aecorationViewFrame?.origin.x)! + 5
        var y:CGFloat = 0
        
        //let row = (collectionView?.numberOfItemsInSection(indexPath.section))! / 3
        
        //let yyy = (160 + (row - 1) * (10 + 100 ) + 20) * indexPath.section
        /*
           貌似item跟row是一样的
        */
        
        let a = (indexPath.item ) % 3
        var row = 0 // 行数
        if(a != 0)
        {
            row = (indexPath.item ) / 3 + 1
        }else
        {
            row = (indexPath.item ) / 3
        }
        
        if(indexPath.item > 0)
        {
            if((indexPath.item - 1)  % 3 == 0) //item 是从1开始第一个cell不算
            {
                x += 0
            }else if((indexPath.item - 1) % 3 == 1 )
            {
                x = x + side + 5
            }else
            {
                x = x + 2 * side + 10
            }
        }
        
        
        
        if(indexPath.item == 0 )//|| indexPath.item == 1 || indexPath.item == 2)
        {
            x += 0
            y = (aecorationViewFrame?.origin.y)! //CGFloat(orignY) + (aecorationViewFrame?.origin.y)!
            //y += orignY + (160 + (row - 1) * (10 + 100 ) + 20) * indexPath.section
            side = (aecorationViewFrame?.size.width)!
            height = 50
            
        }else{
            y = CGFloat(orignY) + CGFloat((height + 5) * (row - 1)) + (aecorationViewFrame?.origin.y)!
            
            //y = (160 + (row - 1) * (10 + 100 ) + 20 ) * indexPath.section
        }
        attributes.frame = CGRectMake(CGFloat(x), CGFloat(y), CGFloat(side), CGFloat(height))
        return attributes
    }
}
