ButtonDesign.swift
==================
import Foundation
import UIKit

@IBDesignable
class OptionsUIButton: UIButton {
    
    @IBInspectable var isButtonSelected: Bool = false {
        didSet {
            if isButtonSelected {
                self.layer.cornerRadius = 5.0
                self.backgroundColor = COLOR_BUTTON_SELECTED_BG
                self.setTitleColor(COLOR_BUTTON_SELECTED_Text, for: .normal)
                self.clipsToBounds = true
            }
            else {
                self.layer.cornerRadius = 5.0
                self.backgroundColor = COLOR_BUTTON_UNSELECTED_BG
                self.setTitleColor(COLOR_BUTTON_UNSELECTED_Text, for: .normal)
                self.clipsToBounds = true
            }
        }
    }
}


extension OptionsUIButton {
    
    override var isSelected: Bool {
        willSet {
        }
        didSet {
            if self.isSelected {
                self.isButtonSelected = true
            } else {
                self.isButtonSelected = false
            }
        }
    }
}


extension OptionsUIButton {
    
    func selectedSelfFrom(_ fromButtons : [OptionsUIButton]) {
        self.isSelected = true
        for btn in fromButtons {
            if self != btn {
                btn.isSelected = false
            }
        }
    }
}
==================
DesignableUITextField.swift
===========================
import Foundation
import UIKit

@IBDesignable
class DesignableUITextField: UITextField {
    /*
     required init(coder aDecoder: NSCoder) {
     super.init(coder: aDecoder)!
     initialSettings()
     }
     */
    
    var picker = UIPickerView()
    var pickerArrayData = [String]()
    var pickerDidSelect : ((Int) -> Void)?
    
    var datePicker = UIDatePicker()
    var strDateFormat = String()
    var datePickerDidSelect : ((Date) -> Void)?

    
    func initialSettings(){
        self.borderStyle = .none
        self.backgroundColor = COLOR_TEXTFIELD_BG
        self.layer.cornerRadius = 5.0
        self.layer.masksToBounds = true
         self.autocorrectionType = .no
    }
    
    @IBInspectable var setBGColor : Bool = true {
        didSet {
            if setBGColor {
                initialSettings()
            }
        }
    }
    
    // Provides left padding for images
    override func leftViewRect(forBounds bounds: CGRect) -> CGRect {
        var textRect = super.leftViewRect(forBounds: bounds)
        textRect.origin.x += leftPadding
        return textRect
    }
    
    override func rightViewRect(forBounds bounds: CGRect) -> CGRect {
        let textRect = super.rightViewRect(forBounds: bounds)

        return textRect
    }
    
    @IBInspectable var leftImage: UIImage? {
        didSet {
            updateView()
        }
    }
    
    
    @IBInspectable var leftPadding: CGFloat = 0 {
        didSet {
            updateView()
        }
    }
    
    @IBInspectable var rightImage: UIImage? {
        didSet {
            updateView()
        }
    }
    
    @IBInspectable var rightPadding: CGFloat = 0 {
        didSet {
            updateView()
        }
    }
    
    //    @IBInspectable var tintColor: UIColor = UIColor.lightGray {
    //        didSet {
    //            updateView()
    //        }
    //    }
    
    func updateView() {
        
        if let image = leftImage {
            leftViewMode = UITextFieldViewMode.always
            let imageView = UIImageView(image: image)
            imageView.image = image
            imageView.contentMode = .center
            //            imageView.frame = CGRect(x: 0, y: 0, width: imageView.frame.size.width + 10.0, height: self.frame.size.height)
            imageView.frame = CGRect(x: 0, y: 0, width: self.frame.size.height, height: self.frame.size.height)
            imageView.backgroundColor = UIColor.clear
            // Note: In order for your image to use the tint color, you have to select the image in the Assets.xcassets and change the "Render As" property to "Template Image".
            //            imageView.tintColor = color
            leftView = imageView
        }
        else if leftPadding == 0 {
            leftViewMode = UITextFieldViewMode.never
            leftView = nil
        }
        else if leftPadding != 0 {
            leftViewMode = UITextFieldViewMode.always
            //            let newLeftView = UIView(frame: CGRect(x: 0, y: 0, width: leftPadding, height: self.frame.size.height))
            let newLeftView = UIView(frame: CGRect(x: 0, y: 0, width: 0, height: 0))
            newLeftView.backgroundColor = UIColor.clear
            newLeftView.isUserInteractionEnabled = false
            leftView = newLeftView
        }
        
        
        if let image = rightImage {
            rightViewMode = UITextFieldViewMode.always
            let imageView = UIImageView(image: image)
            imageView.image = image
            imageView.contentMode = .center
            //            imageView.frame = CGRect(x: 0, y: 0, width: imageView.frame.size.width + 10.0 , height: self.frame.size.height)
            imageView.frame = CGRect(x: 0, y: 0, width: self.frame.size.height , height: self.frame.size.height)
            imageView.backgroundColor = UIColor.clear
            //            rightView = imageView
            
            let newRightView = UIView(frame: CGRect(x: 0, y: 0, width: rightPadding + imageView.frame.size.width, height: self.frame.size.height))
            imageView.frame = CGRect(x: newRightView.frame.size.width - imageView.frame.size.width, y: 0, width: self.frame.size.height , height: self.frame.size.height)
            newRightView.addSubview(imageView)
            newRightView.backgroundColor = UIColor.clear
            newRightView.isUserInteractionEnabled = false
            rightView = newRightView
        }
        else if rightPadding == 0 {
            rightViewMode = UITextFieldViewMode.never
            rightView = nil
        }
        else if rightPadding != 0 {
            rightViewMode = UITextFieldViewMode.always
            let newRightView = UIView(frame: CGRect(x: 0, y: 0, width: rightPadding, height: self.frame.size.height))
            //            let newRightView = UIView(frame: CGRect(x: 0, y: 0, width: 0, height: 0))
            newRightView.backgroundColor = UIColor.clear
            newRightView.isUserInteractionEnabled = false
            rightView = newRightView
        }
        
        // Placeholder text color
        //        attributedPlaceholder = NSAttributedString(string: placeholder != nil ?  placeholder! : "", attributes:[NSForegroundColorAttributeName: color])
        
    }
}



//NOTE:-
// BELOW is for Picker View
extension DesignableUITextField : UIPickerViewDataSource, UIPickerViewDelegate {
    
    func addPickerView(_ arrData : [String] ,  callback: ((Int) -> Void)?) {
        pickerArrayData = arrData
        pickerDidSelect = callback
        picker = UIPickerView()
        picker.dataSource = self
        picker.delegate = self
        picker.reloadAllComponents()
        self.inputView = picker
    }
    
    func numberOfComponents(in pickerView: UIPickerView) -> Int {
        return 1
    }
    
    func pickerView(_ pickerView: UIPickerView, numberOfRowsInComponent component: Int) -> Int {
        return pickerArrayData.count
    }
    
    func pickerView(_ pickerView: UIPickerView, titleForRow row: Int, forComponent component: Int) -> String? {
        return pickerArrayData[row]
    }
    
    func pickerView(_ pickerView: UIPickerView, didSelectRow row: Int, inComponent component: Int) {
        //        print("\n************* didSelectRow **********************************\n")
        self.text = pickerArrayData[row]
        if pickerDidSelect != nil {
            pickerDidSelect!(row)
        }
    }
}


extension DesignableUITextField {
    func addDatePicker(mode : UIDatePickerMode, dateFormat : String, callback: ((Date) -> Void)?) {
        datePickerDidSelect = callback
        strDateFormat = dateFormat
        datePicker = UIDatePicker()
        datePicker.datePickerMode = mode
        self.inputView = datePicker
        
        datePicker.addTarget(self, action: #selector(dateChanged(_:)), for: .valueChanged)
    }
    
    func dateChanged(_ sender: UIDatePicker){
        //For date formate
        let formatter = DateFormatter()
        formatter.dateFormat = strDateFormat
        self.text = formatter.string(from: datePicker.date)
        if datePickerDidSelect != nil {
            datePickerDidSelect!(datePicker.date)
        }
    }
}
============================
FormInputNavigation.swift
============================


import Foundation
import UIKit

extension UIViewController {
    func addInputAccessoryForTextFields(textFields: [UITextField],
        previousNextable: Bool = false)
    {
        for (index, textField) in textFields.enumerated() {
            let toolbar: UIToolbar = UIToolbar()
            toolbar.sizeToFit()
            
            var items = [UIBarButtonItem]()
            if previousNextable {
                let previousButton = UIBarButtonItem(image: UIImage(named: "backwordArrow"), style: .plain, target: nil, action: nil)
                previousButton.tintColor = COLOR_COMMON_DARK_ORNAGE
                previousButton.width = 30
                if textField == textFields.first {
                    previousButton.isEnabled = false
                } else {
                    
                    previousButton.target = textFields[index - 1]
                    previousButton.action = #selector(UITextField.becomeFirstResponder)
                }
                
                let nextButton = UIBarButtonItem(image: UIImage(named: "forwordArrow"), style: .plain, target: nil, action: nil)
                nextButton.tintColor = COLOR_COMMON_DARK_ORNAGE
                nextButton.width = 30
                if textField == textFields.last {
                    nextButton.isEnabled = false
                } else {
                    
                    nextButton.target = textFields[index + 1]
                    nextButton.action = #selector(UITextField.becomeFirstResponder)
                }
                
                items.append(contentsOf: [previousButton, nextButton])
            }
            
            let spacer = UIBarButtonItem(barButtonSystemItem: .flexibleSpace, target: nil, action: nil)
            let doneButton = UIBarButtonItem(barButtonSystemItem: .done, target: view, action: #selector(UIView.endEditing))
            items.append(contentsOf: [spacer, doneButton])
            doneButton.tintColor = COLOR_COMMON_DARK_ORNAGE
            toolbar.setItems(items, animated: false)
            textField.inputAccessoryView = toolbar
        }
    }
}
===========================================End POint===========================
textfild =====>DesignableUITextField
====================TEXTFIELD METHOD================
self.addInputAccessoryForTextFields(textFields: [txtSumAssured, txtpincode], previousNextable: true)
===============UIBUTTON============
uibutton======>OptionsUIButton
================UIBUTTON METHOD=================
btnOneTime.selectedSelfFrom([btnOneTime,btnAnnurally])
sender.selectedSelfFrom([btnOneTime,btnAnnurally])
        if btnAnnurally.isSelected == true {
                    }
        else{
           
        }
===============================================
if sender == self.btnCheck {
            
            btnCheck.isSelected = !btnCheck.isSelected
            
            if btnCheck.isSelected  {
                btnCheck.setImage(#imageLiteral(resourceName: "check"), for: .normal)
                
            }else{
                btnCheck.setImage(#imageLiteral(resourceName: "uncheck"), for: .normal)
                
            }
        }
        
 =============================================
 if sender == self.btnCheck {
            
            btnCheck.isSelected = !btnCheck.isSelected
            
            if btnCheck.isSelected  {
                btnCheck.setImage(#imageLiteral(resourceName: "check"), for: .normal)
                
            }else{
                btnCheck.setImage(#imageLiteral(resourceName: "uncheck"), for: .normal)
                
            }
        }
============================================DATE PICKER
txtDate.addDatePicker(mode: .date, dateFormat: "dd/MM/yyyy") { (resultDate : Date) in
            print(resultDate)
        }
 ====================
 
        

