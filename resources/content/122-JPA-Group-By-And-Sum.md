title=JPA, JPQL, GROUP BY and SUM - The argument must be numeric! 
subtitle=Integer vs. int
created=2013-11-24T03:12:32
tags=JavaEE, JPA, Java, JPQL, Troubleshooting 
language=en

I got following error for one of my JPA queries on GF4 with EclipseLink:

	Exception Description: Error encountered when building the @NamedQuery [AccountingTimeDetail#groupByTitleAndUser] from entity class [class org.eclipse.persistence.internal.jpa.metadata.queries.NamedQueryMetadata].
	Internal Exception: java.lang.ClassCastException: org.eclipse.persistence.jpa.jpql.parser.NullExpression cannot be cast to org.eclipse.persistence.jpa.jpql.parser.IdentificationVariable

Netbeans gave me a more friendly message, but still an error message...

	AccountingTimeDetail#groupByTitleAndUser[46;61]: The argument must be numeric.

This was a little bit strange for me on the first view. My entity beans looks quite good.

	@Entity
	@NamedQueries({
	    @NamedQuery(name = AccountingTimeDetail.groupByTitleAndUser,
	            query = "SELECT pm.title, atd.user, atd.priceHour, SUM(atd.workingTime) FROM AccountingTimeDetail atd, ProjectMember pm WHERE atd.user = pm.individual AND atd.accountingPeriod = :AccountingTimeDetailqueryParam_accountingPeriod GROUP BY pm.title, atd.user, atd.priceHour"
	    )})
	public class AccountingTimeDetail implements Serializable {
	
	    private static final long serialVersionUID = 1L;
	
	    public static final String groupByTitleAndUser = "AccountingTimeDetail#groupByTitleAndUser";
	    public static final String queryParam_accountingPeriod = "accountingPeriod";
	  
	    @Id
	    @GeneratedValue(strategy = GenerationType.AUTO)
	    private Long id;
	
	    private Integer workingTime;

		...

Normally I would say a simple *JOIN* with a *GROUP BY* clause. But *SUM* caused the problem in this case.

>SUM can only be used for numeric values! All right!

But in this case **Integer** is not a numeric value! Integer is an object, that can also be *null*. 

Changing my attribute `workingTime` from **Integer** to **int** everything works fine!!!