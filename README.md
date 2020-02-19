SKIPCHECK;
#Only Calc for Current and Future Years
[{'Working Budget','Working Forecast'}] =N:
	IF(SUBST(!Periods Monthly,1,5)@= 'Input',STET,CONTINUE);

#Only Calc for Current and Future Years
[{'Working Budget','Working Forecast'}] =N:								
	IF (NUMBR(!Periods Monthly) >= NUMBR( DB('Global Assumptions', !Scenarios, 'Current Year') | DB('Global Assumptions', !Scenarios, 'Current Month')  ) ,CONTINUE, 0);

#Merit
[{'Working Budget', 'Working Forecast'},'Merit Increase']= N:
	  IF( NUMBR(!Periods Monthly) >=  NUMBR( DB('Global Assumptions', !Scenarios, 'Current Year') | DB('Labor Assumptions',!Scenarios ,ATTRS('Periods Monthly',!Periods Monthly,'Year'),!Properties,!Positions,'Merit Month')) ,
	   ['Base Salary']* DB('Labor Assumptions',!Scenarios,ATTRS('Periods Monthly',!Periods Monthly,'Year'),!Properties,!Positions,'Merit Rate'), 0);


# VACATION RATE
[{'Working Budget', 'Working Forecast'},'Vacation','No Employee','No Positions']=N:
	DB('Planning',!Scenarios,!Properties, 'Input ' | !Periods Monthly,!Accounts,'Rate')/100 ;

# VACATION CALC
[{'Working Budget', 'Working Forecast'},'Vacation']=N:
     IF( !Accounts@='54204.' |	  ATTRS('Employees',!Employees,'Department'),										
               [ 'Salaries' , 'Base Salary' ] * DB('Labor Summary',!Scenarios,!Properties,!Periods Monthly,!Accounts,'No Employee','No Positions',!Measures Labor Summary)	
     ,CONTINUE );	

#Backout Vacation
[{'Working Budget', 'Working Forecast'},'Vacation']=N:
     IF( !Accounts@= ATTRS('Positions',!Positions,'Accounts'),
              DB('Labor Summary',!Scenarios,!Properties,!Periods Monthly,'54204.' |	 ATTRS('Employees',!Employees,'Department'),!Employees,!Positions,'Vacation')										
     ,CONTINUE );	

# HOLIDAY RATE
[{'Working Budget', 'Working Forecast'},'Holiday','No Employee','No Positions']=N:
	DB('Planning',!Scenarios,!Properties, 'Input ' | !Periods Monthly,!Accounts,'Rate')/100 ;																													

# Holiday CALC
[{'Working Budget', 'Working Forecast'},'Holiday']=N:
     IF( !Accounts@='54208.' |	  ATTRS('Employees',!Employees,'Department'),												
               [ 'Salaries' , 'Base Salary'] * DB('Labor Summary',!Scenarios,!Properties,!Periods Monthly,!Accounts,'No Employee','No Positions',!Measures Labor Summary)	
     ,CONTINUE );	

#Backout Holiday     
[{'Working Budget', 'Working Forecast'},'Holiday']=N:
     IF( !Accounts@= ATTRS('Positions',!Positions,'Accounts'),
              DB('Labor Summary',!Scenarios,!Properties,!Periods Monthly,'54208.' |	 ATTRS('Employees',!Employees,'Department'),!Employees,!Positions,'Holiday')										
     ,CONTINUE );	
     
# SICK RATE
[{'Working Budget', 'Working Forecast'},'Sick','No Employee','No Positions']=N:
	DB('Planning',!Scenarios,!Properties, 'Input ' | !Periods Monthly,!Accounts,'Rate')/100 ;

# Sick CALC
[{'Working Budget', 'Working Forecast'},'Sick']=N:
     IF( !Accounts@='54212.' |	  ATTRS('Employees',!Employees,'Department'),													
               [ 'Salaries' , 'Base Salary'] * DB('Labor Summary',!Scenarios,!Properties,!Periods Monthly,!Accounts,'No Employee','No Positions',!Measures Labor Summary)	
     ,CONTINUE );	

#Backout Sick
[{'Working Budget', 'Working Forecast'},'Sick']=N:
     IF( !Accounts@= ATTRS('Positions',!Positions,'Accounts'),
              DB('Labor Summary',!Scenarios,!Properties,!Periods Monthly,'54212.' |	 ATTRS('Employees',!Employees,'Department'),!Employees,!Positions,'Sick')										
     ,CONTINUE );																																			

#Base Salary
[{'Working Budget', 'Working Forecast'},'Base Salary']= N:
	IF(  DB('Labor Summary',!Scenarios,'All Companies','Input ' |ATTRS('Periods Monthly',!Periods Monthly,'Year'),'No Account',!Employees,'No Positions','Allocation Percent')<>0,
	     ( DB('Labor Summary',!Scenarios,!Properties,'Input ' |ATTRS('Periods Monthly',!Periods Monthly,'Year'),'No Account',!Employees,'No Positions','Allocation Percent')/DB('Labor Summary',!Scenarios,'All Companies','Input ' |ATTRS('Periods Monthly',!Periods Monthly,'Year'),'No Account',!Employees,'No Positions','Allocation Percent')) * ['All Companies','Base Salary Pre Allocation'],['Base Salary Pre Allocation']);

[{'Working Budget', 'Working Forecast'},'FTE']=N:
	IF(  DB('Labor Summary',!Scenarios,'All Companies','Input ' |ATTRS('Periods Monthly',!Periods Monthly,'Year'),'No Account',!Employees,'No Positions','Allocation Percent')<>0,
	     ( DB('Labor Summary',!Scenarios,!Properties,'Input ' |ATTRS('Periods Monthly',!Periods Monthly,'Year'),'No Account',!Employees,'No Positions','Allocation Percent')/DB('Labor Summary',!Scenarios,'All Companies','Input ' |ATTRS('Periods Monthly',!Periods Monthly,'Year'),'No Account',!Employees,'No Positions','Allocation Percent')) * ['All Companies','Head Count'],['Head Count']);

[{'Working Budget','Working Forecast'},{'FTE','Headcount'}] =C:
	IF( ELLEV('Periods Monthly', !Periods Monthly) <>0,
	  DB('Labor Summary',!Scenarios,!Properties,ATTRS('Periods Monthly',!Periods Monthly,'Final Month'),!Accounts,!Employees,!Positions,!Measures Labor Summary),
     CONTINUE);

#Bonus 
[{'Working Budget', 'Working Forecast'},'Bonus'] = N:
     IF( !Accounts@='54178.' |	  ATTRS('Employees',!Employees,'Department'),
               [ 'Management Salaries' , 'Base Salary'] * DB('Labor Assumptions',!Scenarios,ATTRS('Periods Monthly',!Periods Monthly,'Year'),!Properties,!Positions,'Bonus Rate')
     ,CONTINUE );

#Set Dimensionality
[{'Working Budget', 'Working Forecast'}]=N:
	IF( DB('}ElementAttributes_Positions',!Positions,'IDName') @= DB('Labor Input',!Scenarios,ATTRS('Periods Monthly',!Periods Monthly,'Year'),ATTRS('Accounts',!Accounts,'Department') ,!Properties,!Employees, 'Job Title' ) & !Accounts @=ATTRS('Positions',!Positions,'Accounts'), CONTINUE, STET);

#Base Salary Pre Allocation
[{'Working Budget', 'Working Forecast'},'Base Salary Pre Allocation']=N:
	IF( ( DB('Labor Input',!Scenarios,ATTRS('Periods Monthly',!Periods Monthly,'Year'),ATTRS('Accounts',!Accounts,'Department'),!Properties,!Employees,'Term Period') @> (!Periods Monthly) %
	DB('Labor Input',!Scenarios,ATTRS('Periods Monthly',!Periods Monthly,'Year'),ATTRS('Accounts',!Accounts,'Department'),!Properties,!Employees,'Term Period') @= '') &
	DB('Labor Input',!Scenarios,ATTRS('Periods Monthly',!Periods Monthly,'Year'),ATTRS('Accounts',!Accounts,'Department'),!Properties,!Employees,'Start Period') @<= (!Periods Monthly),
	DB('Labor Input',!Scenarios,ATTRS('Periods Monthly',!Periods Monthly,'Year'),ATTRS('Accounts',!Accounts,'Department'),!Properties,!Employees,'Hourly Pay Rate')  *8 * ATTRN('Periods Monthly',!Periods Monthly,'Working Days'),0);


#HeadCount
[{'Working Budget', 'Working Forecast'},'Head Count']=N:
	IF( ['Base Salary']>0,1,0);



FEEDERS;

[{'Working Budget', 'Working Forecast'},{'Base Salary Pre Allocation'}]=>
	['Base Salary'];

[{'Working Budget', 'Working Forecast'},'Base Salary']=>
	['Merit Increase'],['Head Count'],['Bonus'],['Vacation'],['Holiday'],['Sick'],['FTE'];

[{'Working Budget', 'Working Forecast'},'No Account','No Positions','Allocation Percent']=>
	DB('Labor Summary',!Scenarios,!Properties,ATTRS('Periods Monthly',!Periods Monthly,'Year'),ATTRS('Positions',ATTRS('Employees',!Employees,'Position'),'Accounts'),!Employees,ATTRS('Employees',!Employees,'Position'),'Base Salary'),
	DB('Labor Summary',!Scenarios,!Properties,ATTRS('Periods Monthly',!Periods Monthly,'Year'),ATTRS('Positions',ATTRS('Employees',!Employees,'Position'),'Accounts'),!Employees,ATTRS('Employees',!Employees,'Position'),'FTE');

 [{'Working Budget', 'Working Forecast'} ,  'Base Salary', 'Salaries'] => 
	DB('Labor Summary',!Scenarios, !Properties , !Periods Monthly, '54204.' | ATTRS( 'Employees' , !Employees , 'Department' ) ,!Employees  , !Positions ,'Vacation'),
	DB('Labor Summary',!Scenarios, !Properties , !Periods Monthly, '54208.' | ATTRS( 'Employees' , !Employees , 'Department' ) ,!Employees  , !Positions ,'Holiday'),
	DB('Labor Summary',!Scenarios, !Properties , !Periods Monthly, '54212.' | ATTRS( 'Employees' , !Employees , 'Department' ) ,!Employees  , !Positions ,'Sick'),
	DB('Labor Summary',!Scenarios, !Properties , !Periods Monthly, '54178.' | ATTRS( 'Employees' , !Employees , 'Department' ) ,!Employees  , !Positions ,'Bonus');

#External Feeders
 [{'Working Budget', 'Working Forecast'} ,'Total Employees','Total Positions',{'Vacation','Sick','Holiday','Total Salary','Bonus'}]=>
	DB('Planning',!Scenarios,!Properties,'Input ' | !Periods Monthly,!Accounts,'Amount Monthly');	

	
 [{'Working Budget', 'Working Forecast'} ,'54164.50','Total Employees','Total Positions', 'FTE']=>
	DB('Planning',!Scenarios,!Properties,'Input ' | !Periods Monthly,'93339.50','Amount Monthly');	

[{'Working Budget', 'Working Forecast'} ,'Total Employees','Total Positions', 'FTE']=>
	DB('Planning',!Scenarios,!Properties,'Input ' | !Periods Monthly,DB('Planning Assumptions', 'Working Budget',ATTRS ('PROPERTIES', !PROPERTIES, 'PLANNING ASSUMPTION REFERENCE' ),!Accounts, 'Input '| ATTRS('Periods Monthly',!Periods Monthly,'Year'),'Reference Account'),'Amount Monthly');


																									
