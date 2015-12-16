# stage-1--fourth-sprint
第一阶段第四次总结：前期的内容搞好后，我们开始对题目的答案的判断，我们用到的是正则表达式进行判断，如下代码，public static String parseExp(String expression){ 
        //String numberReg="^((?!0)\\d+(\\.\\d+(?<!0))?)|(0\\.\\d+(?<!0))$"; 
        expression=expression.replaceAll("\\s+", "").replaceAll("^\\((.+)\\)$", "$1"); 
        String checkExp="\\d";
        Double db11;
        String minExp="^((\\d+(\\.\\d+)?)|(\\[\\-\\d+(\\.\\d+)?\\]))[\\+\\-\\*\\/]((\\d+(\\.\\d+)?)|(\\[\\-\\d+(\\.\\d+)?\\]))$"; 
        //最小表达式计算 
        if(expression.matches(minExp)){ 
            String result=calculate(expression); 
            System.out.println("+"+result);
            return Double.parseDouble(result)>=0?result:"["+result+"]"; 
        } 
        //计算不带括号的四则运算 
        String noParentheses="^[^\\(\\)]+$"; 
        String priorOperatorExp="(((\\d+(\\.\\d+)?)|(\\[\\-\\d+(\\.\\d+)?\\]))[\\*\\/]((\\d+(\\.\\d+)?)|(\\[\\-\\d+(\\.\\d+)?\\])))"; 
        String operatorExp="(((\\d+(\\.\\d+)?)|(\\[\\-\\d+(\\.\\d+)?\\]))[\\+\\-]((\\d+(\\.\\d+)?)|(\\[\\-\\d+(\\.\\d+)?\\])))"; 
        if(expression.matches(noParentheses)){ 
            Pattern patt=Pattern.compile(priorOperatorExp); 
            Matcher mat=patt.matcher(expression); 
            if(mat.find()){ 
                String tempMinExp=mat.group(); 
                expression=expression.replaceFirst(priorOperatorExp, parseExp(tempMinExp)); 
            }else{ 
                patt=Pattern.compile(operatorExp); 
                mat=patt.matcher(expression); 
                 
                if(mat.find()){ 
                    String tempMinExp=mat.group(); 
                    expression=expression.replaceFirst(operatorExp, parseExp(tempMinExp)); 
                } 
            } 
            return parseExp(expression); 
        } 
        //计算带括号的四则运算 
        String minParentheses="\\([^\\(\\)]+\\)"; 
        Pattern patt=Pattern.compile(minParentheses); 
        Matcher mat=patt.matcher(expression); 
        if(mat.find()){ 
            String tempMinExp=mat.group(); 
            expression=expression.replaceFirst(minParentheses, parseExp(tempMinExp)); 
        } 
        return parseExp(expression); 
    }

    public static double arithmetic(String exp){ 
        String result1 = parseExp(exp).replaceAll("[\\[\\]]", ""); 
        Double db11=Double.parseDouble(result1);
        DecimalFormat df=new DecimalFormat("######0.00"); 
        String result=String.valueOf(df.format(db11));
        return Double.parseDouble(result); 
    } 
    
    
	public static String calculate(String exp){ 
        exp=exp.replaceAll("[\\[\\]]", ""); 
        String number[]=exp.replaceFirst("(\\d)[\\+\\-\\*\\/]", "$1,").split(","); 
        BigDecimal number1=new BigDecimal(number[0]); 
        BigDecimal number2=new BigDecimal(number[1]); 
        BigDecimal number3=new BigDecimal("0");
        BigDecimal result=null; 
         
        String operator=exp.replaceFirst("^.*\\d([\\+\\-\\*\\/]).+$", "$1"); 
        if("+".equals(operator)){ 
            result=number1.add(number2); 
        }else if("-".equals(operator)){ 
            result=number1.subtract(number2); 
        }else if("*".equals(operator)){ 
            result=number1.multiply(number2); 
        }else if("/".equals(operator)){ 
        	try
        	{
            result=number1.divide(number2,5,BigDecimal.ROUND_HALF_UP); 
        	}catch(ArithmeticException e)
        	{
        		result=number3;
        	}
            
        } 
         
        return result!=null?result.toString():null; 
    }
