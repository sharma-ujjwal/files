```
package sunlife.bm.dc.onlineordering.ejb.slsb;

import java.rmi.RemoteException;
import java.util.Date;

import javax.ejb.EJBException;
import javax.ejb.SessionBean;
import javax.ejb.SessionContext;
import javax.transaction.SystemException;
import javax.transaction.UserTransaction;

import org.apache.log4j.Logger;

import sunlife.us.dc.log.LogFactory;
import sunlife.bm.dc.onlineordering.appservice.OnlineOrderingAppService;
import sunlife.bm.dc.onlineordering.common.ClientException;
import sunlife.bm.dc.onlineordering.common.OLOException;
import sunlife.bm.dc.onlineordering.to.CategoryTO;
import sunlife.bm.dc.onlineordering.to.CategoryValueTO;
import sunlife.bm.dc.onlineordering.to.ConsumerTO;
import sunlife.bm.dc.onlineordering.to.CountryTO;
import sunlife.bm.dc.onlineordering.to.EmailOrderTO;
import sunlife.bm.dc.onlineordering.to.FirmKeyItemsTO;
import sunlife.bm.dc.onlineordering.to.FirmTO;
import sunlife.bm.dc.onlineordering.to.FormTO;
import sunlife.bm.dc.onlineordering.to.GridViewTO;
import sunlife.bm.dc.onlineordering.to.ItemDetailTO;
import sunlife.bm.dc.onlineordering.to.ItemSummaryTO;
import sunlife.bm.dc.onlineordering.to.OrderDetailTO;
import sunlife.bm.dc.onlineordering.to.OrderSummaryTO;
import sunlife.bm.dc.onlineordering.to.PDFMergeTO;
import sunlife.bm.dc.onlineordering.to.RequestTO;
import sunlife.bm.dc.onlineordering.to.ResponseTO;
import sunlife.bm.dc.onlineordering.to.SearchResultDetailsTO;
import sunlife.bm.dc.onlineordering.to.SearchWizardTO;
import sunlife.bm.dc.onlineordering.to.StateProvinceTO;
import sunlife.bm.dc.onlineordering.to.WizardTabTO;

/**
 * OnlineOrderingServicesBean
 * 
 * Created on May 16, 2009
 * 
 * @author <a href="mailto:jonathan.mcdonald@sunlife.com">Jonathan McDonald (YU33)</a>
 *         <p>
 *         Copyright 2009, Sun Life Financial
 *         </p>
 */
public class OnlineOrderingServicesBean implements SessionBean {
    private static Logger logger = LogFactory.getLogger(OnlineOrderingServicesBean.class.getName());

    /**
     * Default Serial UID.
     */
    private static final long serialVersionUID = 1L;
    
    /**
     * Reference to SessionContext. For Session beans.
     */
    private SessionContext context;
    
    /**
     * Number of times to retry the transaction if deadlock occurs
     */
    private static final int MAX_LOCKING_ATTEMPT = 2;

    /**
     * Reference to our plain java application service.
     */
    OnlineOrderingAppService svc = new OnlineOrderingAppService();

    public ResponseTO checkoutEmail(RequestTO requestTO) throws ClientException {
        logger.debug("[START] checkoutEmail()");
        
        UserTransaction ut = context.getUserTransaction();
        ResponseTO responseTO = null;
        
        try {
        	for (int i = 1; i <= MAX_LOCKING_ATTEMPT; i++) {
            	//begin bean managed transaction
            	logger.debug("Starting transaction");   
            	
            	ut.begin();
            	responseTO = svc.checkoutEmail(requestTO);
            	
            	//retry if deadlock detected.
            	if (responseTO != null && responseTO.isDeadLocked()) {
            		//transaction is deadlocked. Roll it back and try again.
            		ut.rollback();
            		logger.debug("Deadlock detected in checkoutEmail(). " +
            				"Rolled back transaction and trying again. Number of attempts: " + i);
            		if (i == MAX_LOCKING_ATTEMPT) {
            			ut = null;
            			throw new EJBException("Deadlock detected in checkoutEmail()");
            		}
            	} else {
                  	//inserts successful so end bean managed transaction
                	ut.commit();  
                	logger.debug("No Deadlock detected in checkoutEmail(). Transaction committed");  
                	break; // exit out of for loop
            	}          		
        	} //for    
        	return responseTO;       	
        } catch (Exception e) {
            logger.error("checkoutEmail() - Rolling Back Transaction", e);
            if (ut != null) {
            	try {
            		//transaction failed, so rollback.
					ut.rollback();
					logger.debug("Transaction rolled back.");
				} catch (SystemException se) {
					logger.error("Exception while rolling back transaction for checkoutEmail()", se);
				}       	
            }
            throw new EJBException(e.getMessage());
        } finally {
            logger.debug("[END] checkoutEmail()");
        }
    }

    public ResponseTO checkoutShip(RequestTO requestTO) throws ClientException {
        logger.debug("[START] checkoutShip()");
        
        UserTransaction ut = context.getUserTransaction();
        ResponseTO responseTO = null;
        
        try {
        	for (int i = 1; i <= MAX_LOCKING_ATTEMPT; i++) {
            	//begin bean managed transaction
            	logger.debug("Starting transaction");
            	
            	ut.begin();
            	responseTO = svc.checkoutShip(requestTO);
            	
            	//retry if deadlock detected.
            	if (responseTO != null && responseTO.isDeadLocked()) {
            		//transaction is deadlocked. Roll it back and try again.
            		ut.rollback();
            		logger.debug("Deadlock detected in checkoutShip(). " +
            				"Rolled back transaction and trying again. Number of attempts: " + i);
            		if (i == MAX_LOCKING_ATTEMPT) {
            			ut = null;
            			throw new EJBException("Deadlock detected in checkoutShip()");
            		}
            	} else {
                  	//inserts successful so end bean managed transaction
                	ut.commit();  
                	logger.debug("No Deadlock detected in checkoutShip(). Transaction committed");  
                	break; // exit out of for loop
            	}            	        		
        	}//for    
        	return responseTO;
        } catch (Exception e) {
            logger.error("checkoutShip() - Rolling Back Transaction", e);
            if (ut != null) {
            	try {
            		//transaction failed, so rollback.
					ut.rollback();
					logger.debug("Transaction rolled back.");
				} catch (SystemException se) {
					logger.error("Exception while rolling back transaction for checkoutShip()", se);
				}       	
            }
            throw new EJBException(e.getMessage());
        } finally {
            logger.debug("[END] checkoutShip()");
        }
    }

    public ResponseTO checkoutShipPrvAddr(RequestTO requestTO) throws ClientException {
        logger.debug("[START] checkoutShipPrvAddr()");
        
        UserTransaction ut = context.getUserTransaction();
        ResponseTO responseTO = null;
        
        try {
        	for (int i = 1; i <= MAX_LOCKING_ATTEMPT; i++) {
            	//begin bean managed transaction
            	logger.debug("Starting transaction");
            	
            	ut.begin();
            	responseTO = svc.checkoutShipPrvAddr(requestTO);
            	
            	//retry if deadlock detected.
            	if (responseTO != null && responseTO.isDeadLocked()) {
            		//transaction is deadlocked. Roll it back and try again.
            		ut.rollback();
            		logger.debug("Deadlock detected in checkoutShipPrvAddr(). " +
            				"Rolled back transaction and trying again. Number of attempts: " + i);
            		if (i == MAX_LOCKING_ATTEMPT) {
            			ut = null;
            			throw new EJBException("Deadlock detected in checkoutShipPrvAddr()");
            		}
            	} else {
                  	//inserts successful so end bean managed transaction
                	ut.commit();  
                	logger.debug("No Deadlock detected in checkoutShipPrvAddr(). Transaction committed");  
                	break; // exit out of for loop
            	}            	        		
        	}//for    
        	return responseTO;
        } catch (Exception e) {
            logger.error("checkoutShipPrvAddr() - Rolling Back Transaction", e);
            if (ut != null) {
            	try {
            		//transaction failed, so rollback.
					ut.rollback();
					logger.debug("Transaction rolled back.");
				} catch (SystemException se) {
					logger.error("Exception while rolling back transaction for checkoutShipPrvAddr()", se);
				}       	
            }
            throw new EJBException(e.getMessage());
        } finally {
            logger.debug("[END] checkoutShipPrvAddr()");
        }
    }

    public ItemSummaryTO[] deleteFavorite(RequestTO requestTO) throws ClientException {
        logger.debug("[START] deleteFavorite()");
        
        UserTransaction ut = context.getUserTransaction();
        
        try {
        	//begin bean managed transaction
        	logger.debug("Starting transaction");
        	ut.begin();  
        	ItemSummaryTO[] arrItemSummaryTO = svc.deleteFavorite(requestTO);
        	//inserts successful so end bean managed transaction
        	ut.commit();
        	logger.debug("Transaction committed");
            return arrItemSummaryTO;
        } catch (Exception e) {
            logger.error("deleteFavorite() - Rolling Back Transaction", e);
            if (ut != null) {
            	try {
            		//transaction failed, so rollback.
					ut.rollback();
					logger.debug("Transaction rolled back.");
				} catch (SystemException se) {
					logger.error("Exception while rolling back transaction for deleteFavorite()", se);
				}       	
            }
            throw new EJBException(e.getMessage());
        } finally {
            logger.debug("[END] deleteFavorite()");
        }
    }

    public void deleteOrder(RequestTO requestTO) throws ClientException {
        logger.debug("[START] deleteOrder()");
        
        UserTransaction ut = context.getUserTransaction();
        
        try {
        	//begin bean managed transaction
        	logger.debug("Starting transaction");
        	ut.begin();
            svc.deleteOrder(requestTO);
        	//inserts successful so end bean managed transaction
        	ut.commit();
        	logger.debug("Transaction committed");
        } catch (Exception e) {
            logger.error("deleteOrder() - Rolling Back Transaction", e);
            if (ut != null) {
            	try {
            		//transaction failed, so rollback.
					ut.rollback();
					logger.debug("Transaction rolled back.");
				} catch (SystemException se) {
					logger.error("Exception while rolling back transaction for deleteOrder()", se);
				}       	
            }
            throw new EJBException(e.getMessage());
        } finally {
            logger.debug("[END] deleteOrder()");
        }
    }

    public void ejbActivate() throws EJBException, RemoteException {
        // TODO Auto-generated method stub
    }

    public void ejbCreate() {
    }

    public void ejbPassivate() throws EJBException, RemoteException {
        // TODO Auto-generated method stub
    }

    public void ejbRemove() throws EJBException, RemoteException {
        // TODO Auto-generated method stub
    }

    public WizardTabTO[] findApplicationTabs(RequestTO requestTO) throws ClientException {
        logger.debug("[START] findApplicationTabs()");
        try {
            return svc.findApplicationTabs(requestTO);
        } catch (OLOException oloEx) {
            logger.error("findApplicationTabs()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findApplicationTabs()");
        }
    }

    public SearchWizardTO findAppropriateSearchWizard(RequestTO requestTO) throws ClientException {
        logger.debug("[START] findAppropriateSearchWizard()");
        try {
            return svc.findAppropriateSearchWizard(requestTO);
        } catch (OLOException oloEx) {
            logger.error("findAppropriateSearchWizard()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findAppropriateSearchWizard()");
        }
    }

    public ResponseTO findCategoryResults(RequestTO requestTO) throws ClientException {
        logger.debug("[START] findCategoryResults()");
        try {
            return svc.findCategoryResults(requestTO);
        } catch (OLOException oloEx) {
            logger.error("findCategoryResults()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findCategoryResults()");
        }
    }

    public CategoryTO[] findCategoryValues(RequestTO requestTO) throws ClientException {
        logger.debug("[START] findCategoryValues()");
        try {
            return svc.findCategoryValues(requestTO);
        } catch (OLOException oloEx) {
            logger.error("findCategoryValues()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findCategoryValues()");
        }
    }

    public ItemSummaryTO[] findCheckoutItemsEmail(RequestTO requestTO) throws ClientException {
        logger.debug("[START] findCheckoutItemsEmail()");
        try {
            return svc.findCheckoutItemsEmail(requestTO);
        } catch (OLOException oloEx) {
            logger.error("findCheckoutItemsEmail()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findCheckoutItemsEmail()");
        }
    }

    public ResponseTO findCheckoutItemsShip(RequestTO requestTO) throws ClientException {
        logger.debug("[START] findCheckoutItemsShip()");
        try {
            return svc.findCheckoutItemsShip(requestTO);
        } catch (OLOException oloEx) {
            logger.error("findCheckoutItemsShip()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findCheckoutItemsShip()");
        }
    }

    public CountryTO[] findCountries() throws ClientException {
        logger.debug("[START] findCountries()");
        try {
            return svc.findCountries();
        } catch (OLOException oloEx) {
            logger.error("findCountries()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findCountries()");
        }
    }

    public ItemSummaryTO[] findFavouriteItems(RequestTO requestTO) throws ClientException {
        logger.debug("[START] findFavouriteItems()");
        try {
            return svc.findFavouriteItems(requestTO);
        } catch (OLOException oloEx) {
            logger.error("findFavouriteItems()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findFavouriteItems()");
        }
    }

    public FirmKeyItemsTO[] findFirmItems(RequestTO requestTO) throws ClientException {
        logger.debug("[START] findFirmItems()");
        try {
            return svc.findFirmItems(requestTO);
        } catch (OLOException oloEx) {
            logger.error("findFirmItems()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findFirmItems()");
        }
    }

    public FirmTO[] findFirmsToImpersonate(RequestTO requestTO) throws ClientException {
        logger.debug("[START] findFirmsToImpersonate()");
        try {
            return svc.findFirmsToImpersonate(requestTO);
        } catch (OLOException oloEx) {
            logger.error("findStates()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findFirmsToImpersonate()");
        }
    }

    public ItemSummaryTO[] findFundPerformanceItems(final String applicationId, final String sbuCd,
            final String pubprvInd, final String prodVers, final String invvId) throws ClientException {
        logger.debug("[START] findFundPerformanceItems()");
        try {
            return svc.findFundPerformanceItems(applicationId, sbuCd, pubprvInd, prodVers, invvId);
        } catch (OLOException oloEx) {
            logger.error("findFundPerformanceItems()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findFundPerformanceItems()");
        }
    }

    public ItemSummaryTO[] findHighlightedItems(final RequestTO requestTO) throws ClientException {
        logger.debug("[START] findHighlightedItems()");
        try {
            return svc.findHighlightedItems(requestTO);
        } catch (OLOException oloEx) {
            logger.error("findHighlightedItems()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findHighlightedItems()");
        }
    }

    public ItemDetailTO findItemDetails(RequestTO requestTO) throws ClientException {
        logger.debug("[START] findItemDetails()");
        try {
            return svc.findItemDetails(requestTO);
        } catch (OLOException oloEx) {
            logger.error("findItemDetails()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findItemDetails()");
        }
    }

    public ItemSummaryTO findItemSummary(RequestTO requestTO) throws ClientException {
        logger.debug("[START] findItemSummary()");
        try {
            return svc.findItemSummary(requestTO);
        } catch (OLOException oloEx) {
            logger.error("findItemSummary()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findItemSummary()");
        }
    }

    public GridViewTO[] findLibraryValues(RequestTO requestTO) throws ClientException {
        logger.debug("[START] findLibraryValues()");
        try {
            return svc.findLibraryValues(requestTO);
        } catch (OLOException oloEx) {
            logger.error("findLibraryValues()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findLibraryValues()");
        }
    }

    public OrderSummaryTO findOrderByOrderNumber(RequestTO requestTO) throws ClientException {
        logger.debug("[START] findOrderByOrderNumber()");
        try {
            return svc.findOrderByOrderNumber(requestTO);
        } catch (OLOException oloEx) {
            logger.error("findOrderByOrderNumber()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findOrderByOrderNumber()");
        }
    }

    public OrderDetailTO findOrderDetails(RequestTO requestTO) throws ClientException {
        logger.debug("[START] findOrderDetails()");
        try {
            return svc.findOrderDetails(requestTO);
        } catch (OLOException oloEx) {
            logger.error("findOrderDetails()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findOrderDetails()");
        }
    }

    public ItemSummaryTO[] findOrderItems(RequestTO requestTO) throws ClientException {
        logger.debug("[START] findOrderItems()");
        try {
            return svc.findOrderItems(requestTO);
        } catch (OLOException oloEx) {
            logger.error("findOrderItems()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findOrderItems()");
        }
    }

    public OrderSummaryTO[] findOrdersByExternalConsumerId(RequestTO requestTO) throws ClientException {
        logger.debug("[START] findOrdersByExternalConsumerId()");
        try {
            return svc.findOrdersByExternalConsumerId(requestTO);
        } catch (OLOException oloEx) {
            logger.error("findOrdersByExternalConsumerId()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findOrdersByExternalConsumerId()");
        }
    }

    public OrderSummaryTO[] findOrdersByInternalACF2(RequestTO requestTO) throws ClientException {
        logger.debug("[START] findOrdersByInternalACF2()");
        try {
            return svc.findOrdersByInternalACF2(requestTO);
        } catch (OLOException oloEx) {
            logger.error("findOrdersByInternalACF2()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findOrdersByInternalACF2()");
        }
    }

    public OrderSummaryTO[] findOrdersByInternalCreator(RequestTO requestTO) throws ClientException {
        logger.debug("[START] findOrdersByInternalCreator()");
        try {
            return svc.findOrdersByInternalCreator(requestTO);
        } catch (OLOException oloEx) {
            logger.error("findOrdersByInternalCreator()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findOrdersByInternalCreator()");
        }
    }

    public OrderSummaryTO[] findOrdersByInternalDistributor(RequestTO requestTO) throws ClientException {
        logger.debug("[START] findOrdersByInternalDistributor()");
        try {
            return svc.findOrdersByInternalDistributor(requestTO);
        } catch (OLOException oloEx) {
            logger.error("findOrdersByInternalDistributor()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findOrdersByInternalDistributor()");
        }
    }

    public OrderSummaryTO[] findOrdersByInternalRecipient(RequestTO requestTO) throws ClientException {
        logger.debug("[START] findOrdersByInternalRecipient()");
        try {
            return svc.findOrdersByInternalRecipient(requestTO);
        } catch (OLOException oloEx) {
            logger.error("findOrdersByInternalRecipient()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findOrdersByInternalRecipient()");
        }
    }

    public ItemSummaryTO[] findSearchTextResults(RequestTO requestTO) throws ClientException {
        logger.debug("[START] findSearchTextResults()");
        try {
            return svc.findSearchTextResults(requestTO);
        } catch (OLOException oloEx) {
            logger.error("findSearchTextResults()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findSearchTextResults()");
        }
    }

    public ConsumerTO[] findShipAddresses(RequestTO requestTO) throws ClientException {
        logger.debug("[START] findShipAddresses()");
        try {
            return svc.findShipAddresses(requestTO);
        } catch (OLOException oloEx) {
            logger.error("findShipAddresses()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findShipAddresses()");
        }
    }

    public CategoryTO[] findSiteViewCategories(final String applicationId, final String sbuCd) throws ClientException {
        logger.debug("[START] findSiteViewCategories()");
        try {
            return svc.findSiteViewCategories(applicationId, sbuCd);
        } catch (OLOException oloEx) {
            logger.error("findSiteViewCategories()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findSiteViewCategories()");
        }
    }

    public String findSiteViewCategoryValueDesc(final String applicationId, final String sbuCd,
            final String categoryValueCd) throws ClientException {
        logger.debug("[START] findSiteViewCategoryValueDesc()");
        try {
            return svc.findSiteViewCategoryValueDesc(applicationId, sbuCd, categoryValueCd);
        } catch (OLOException oloEx) {
            logger.error("findSiteViewCategoryValueDesc()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findSiteViewCategoryValueDesc()");
        }
    }

    public CategoryValueTO[] findSiteViewCategoryValues(final String applicationId, final String sbuCd,
            final String categoryCd) throws ClientException {
        logger.debug("[START] findSiteViewCategoryValues()");
        try {
            return svc.findSiteViewCategoryValues(applicationId, sbuCd, categoryCd);
        } catch (OLOException oloEx) {
            logger.error("findSiteViewCategoryValues()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findSiteViewCategoryValues()");
        }
    }

    public ItemSummaryTO[] findSiteViewItems(final String applicationId, final String sbuCd, final String categoryCd,
            final String categoryValueCd) throws ClientException {
        logger.debug("[START] findSiteViewItems()");
        try {
            return svc.findSiteViewItems(applicationId, sbuCd, categoryCd, categoryValueCd);
        } catch (OLOException oloEx) {
            logger.error("findSiteViewItems()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findSiteViewItems()");
        }
    }

    public StateProvinceTO[] findStates() throws ClientException {
        logger.debug("[START] findStates()");
        try {
            return svc.findStates();
        } catch (OLOException oloEx) {
            logger.error("findStates()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findStates()");
        }
    }

    public SearchResultDetailsTO findWizardCategoryValues(RequestTO requestTO) throws ClientException {
        logger.debug("[START] findWizardCategoryValues()");
        try {
            return svc.findWizardCategoryValues(requestTO);
        } catch (OLOException oloEx) {
            logger.error("findWizardCategoryValues()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findWizardCategoryValues()");
        }
    }

    public int insertOrder(RequestTO requestTO) throws ClientException {
        logger.debug("[START] insertOrder()");
        
        UserTransaction ut = context.getUserTransaction();
        
        try {
        	//begin bean managed transaction
        	logger.debug("Starting transaction");
        	ut.begin();
        	int orderNum = svc.insertOrder(requestTO);
        	//inserts successful so end bean managed transaction
        	ut.commit();
        	logger.debug("Transaction committed");
        	return orderNum;
        } catch (Exception e) {
            logger.error("insertOrder() - Rolling Back Transaction", e);
            if (ut != null) {
            	try {
            		//transaction failed, so rollback.
					ut.rollback();
					logger.debug("Transaction rolled back.");
				} catch (SystemException se) {
					logger.error("Exception while rolling back transaction for insertOrder()", se);
				}       	
            }
            throw new EJBException(e.getMessage());
        } finally {
            logger.debug("[END] insertOrder()");
        }
    }

    public void insertOrderItem(RequestTO requestTO) throws ClientException {
        logger.debug("[START] insertOrderItem()");
        
        UserTransaction ut = context.getUserTransaction();
        
        try {
        	//begin bean managed transaction
        	logger.debug("Starting transaction");
        	ut.begin();
            svc.insertOrderItem(requestTO);
        	//inserts successful so end bean managed transaction
        	ut.commit();
        	logger.debug("Transaction committed");
        } catch (Exception e) {
            logger.error("insertOrderItem() - Rolling Back Transaction", e);
            if (ut != null) {
            	try {
            		//transaction failed, so rollback.
					ut.rollback();
					logger.debug("Transaction rolled back.");
				} catch (SystemException se) {
					logger.error("Exception while rolling back transaction for insertOrderItem()", se);
				}       	
            }
            throw new EJBException(e.getMessage());
        } finally {
            logger.debug("[END] insertOrderItem()");
        }
    }

    public PDFMergeTO mergePDFSearch(RequestTO requestTO) throws ClientException {
        logger.debug("[START] mergePDFSearch()");
        try {
            return svc.mergePDFSearch(requestTO);
        } catch (OLOException oloEx) {
            logger.error("mergePDFSearch()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] mergePDFSearch()");
        }
    }

    public boolean saveFavorite(RequestTO requestTO) throws ClientException {
        logger.debug("[START] saveFavorite()");
        
        UserTransaction ut = context.getUserTransaction();
        
        try {
        	//begin bean managed transaction
        	logger.debug("Starting transaction");
        	ut.begin();
        	boolean blItemSavedAsFavourite = svc.saveFavorite(requestTO);
        	//inserts successful so end bean managed transaction
        	ut.commit();
        	logger.debug("Transaction committed");
        	return blItemSavedAsFavourite;
        } catch (Exception e) {
            logger.error("saveFavorite() - Rolling Back Transaction", e);
            if (ut != null) {
            	try {
            		//transaction failed, so rollback.
					ut.rollback();
					logger.debug("Transaction rolled back.");
				} catch (SystemException se) {
					logger.error("Exception while rolling back transaction for saveFavorite()", se);
				}       	
            }
            throw new EJBException(e.getMessage());
        } finally {
            logger.debug("[END] saveFavorite()");
        }
    }

    public EmailOrderTO sendEmailOrder(RequestTO requestTO) throws ClientException {
        logger.debug("[START] sendEmailOrder()");
        try {
            return svc.sendEmailOrder(requestTO.getSbuCd(), requestTO.getUserId(), requestTO.getOrderNum(), requestTO.getArrMergePDFFormIds());
        } catch (OLOException oloEx) {
            logger.error("sendEmailOrder()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] sendEmailOrder()");
        }
    }
    
    /**
     * Added for OLO / Edelivery integration
     * 
     * @param applicationId
     * @param sbuCd
     * @param prodCodeWithVersion
     * @param pubprvInd
     * @param firmId
     * @param formDate
     * @param itemTypeCodes
     * @return
     * @throws ClientException
     */
    public ItemSummaryTO[] findEDeliveryFormItems(final String applicationId, final String sbuCd,
            final String prodCodeWithVersion, final String pubprvInd, final int firmId,
        	final Date formDate, final String[] itemTypeCodes) throws ClientException {
        logger.debug("[START] findEDeliveryFormItems()");
        try {
            return svc.findEDeliveryFormItems(applicationId, sbuCd, prodCodeWithVersion, 
            		pubprvInd, firmId, formDate, itemTypeCodes);
        } catch (OLOException oloEx) {
            logger.error("findEDeliveryFormItems()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findEDeliveryFormItems()");
        }
    }
    
    public ItemSummaryTO[] findATLMFormItems(final String applicationId, final String sbuCd,
            final String prodCodeWithVersion, final String pubprvInd, final String invvId, 
            	final String itemType) throws ClientException {
        logger.debug("[START] findATLMFormItems()");
        try {
            return svc.findATLMFormItems(applicationId, sbuCd, prodCodeWithVersion, 
            		pubprvInd, invvId, itemType);
        } catch (OLOException oloEx) {
            logger.error("findATLMFormItems()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] findATLMFormItems()");
        }
    }
    
    /** 
     * For an array of form ids, determines if each file is locked, i.e. password protected. 
     * 
     * @param arrMergePDFFormIds array of form ids.
     * @return array of FormTOs or null if no forms in form db for form ids.
     * @throws ClientException
     */
    public FormTO[] checkForLockedPDFs(final int[] arrMergePDFFormIds) throws ClientException {
    	logger.debug("[START] checkForLockedPDFs()");
    	try {
            return svc.checkForLockedPDFs(arrMergePDFFormIds); 
        } catch (OLOException oloEx) {
            logger.error("checkForLockedPDFs()", oloEx);
            throw new ClientException(oloEx.getMessage());
        } finally {
            logger.debug("[END] checkForLockedPDFs()");
        }
    }

    public void setSessionContext(SessionContext ctx) throws EJBException, RemoteException {
        context = ctx;
    }
}
```
