The system has a remote code execution vulnerability.  
system url:  
https://gitee.com/mldong/mldong  
https://github.com/garethhk/mldong  
Lock the dangerous function eval() through code search, which usually triggers expression injection. The main function of this code is to execute custom execution logic for decision nodes. 
com/mldong/modules/wf/engine/model/DecisionModel.java  
<img width="415" alt="image" src="https://github.com/biantaibao/mldong_RCE/assets/131763503/08411fd8-38dd-4036-8d54-d2c0bcaa5d1b">  
Through code tracing, it was found that the eval method is implemented based on the ExpressionEngine interface.  
/cn/hutool/extra/expression/ExpressionUtil.class  
<img width="415" alt="image" src="https://github.com/biantaibao/mldong_RCE/assets/131763503/20308419-c227-4b6b-adba-0ccfd758aa84">  
The getEngine method is a static method that will be automatically called when ExpressionUtil is instantiated. Finally, locate the configuration file, and based on the file content, it can be inferred that the eval() method recognizes SPEL expressions.  
mldong-master\mldong-framework\mldong-base\src\main\resources\META-INF\services\cn.hutool.extra.expression.ExpressionEngine  
<img width="416" alt="image" src="https://github.com/biantaibao/mldong_RCE/assets/131763503/5ab3e6c8-2f80-4371-ad0f-b00bf8a06660">  
Vulnerability verification:  
By calling the chain, it is found that the eval() method is triggered starting from the instance startup interface. It is worth noting that it is only triggered when executing the decision model, which means that the processing process needs to have judgment conditions.  
<img width="415" alt="image" src="https://github.com/biantaibao/mldong_RCE/assets/131763503/9a96d7b3-f9e1-42fa-9d8e-54d389224d88">  
Go to the front-end page and click on the add menu.  
<img width="415" alt="image" src="https://github.com/biantaibao/mldong_RCE/assets/131763503/87b2736c-64f2-479b-8c9c-26328670ebb3">  
<img width="415" alt="image" src="https://github.com/biantaibao/mldong_RCE/assets/131763503/4fb25d86-5676-4ede-9d98-906ed0d49a6e">  
After adding, click on the design menu, right-click, and select the leave form. Select the start node, condition judgment, and end node in sequence.  
<img width="415" alt="image" src="https://github.com/biantaibao/mldong_RCE/assets/131763503/7d962c7c-1409-48bf-b1ff-3db6bf8e9ebc">  
<img width="416" alt="image" src="https://github.com/biantaibao/mldong_RCE/assets/131763503/97a75023-9c13-475d-943e-85bf30aa1e84">  
Click on the condition judgment to edit.  
<img width="416" alt="image" src="https://github.com/biantaibao/mldong_RCE/assets/131763503/3e420e3a-41d1-4dc1-9eb7-ab435eb67365">  
Payload：T(java.lang.Runtime).getRuntime().exec('calc')  
Click save  
<img width="416" alt="image" src="https://github.com/biantaibao/mldong_RCE/assets/131763503/22220bba-c287-480e-8629-078ddaba73ff">  
After deployment, initiate the process in the process definition menu.  
<img width="415" alt="image" src="https://github.com/biantaibao/mldong_RCE/assets/131763503/7f39f1d8-5661-4db7-8339-13412b915fdb">  
Successfully triggered vulnerability.  
<img width="415" alt="image" src="https://github.com/biantaibao/mldong_RCE/assets/131763503/723806e5-dac3-47f6-bf96-3eed8c0068cd">
















